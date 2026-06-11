---
title: "Project - Phase IV"
date: 2026-06-03
draft: false
description: "Finishing our project"
slug: "phase4post"
tags: ["project", "Setup"]
authors:
  - "alyssa_haidar"
  - "bina"
  - "charles_chapman"
  - "tyler_gladu"
showAuthorsBadges: false
---

## University Ranking ML Model — Alyssa Haidar

### What the Model Does

The university ranking model is an unsupervised cosine similarity model that takes three student preferences as input and ranks all EU universities in the database by how closely they match. The three features are estimated per-student fees, highest degree offered, and staff FTE as a proxy for campus size.

Cosine similarity measures the angle between two vectors in feature space. A score of 100 means the university points in exactly the same direction as the student's preferences. Negative scores mean it points in the opposite direction. The model ranks all universities by this score and returns the top n results.

### Software Architecture

The model lives in `modelrec.py` as a Python class `university_ranking_model` inside the Flask backend. It has internal helper methods for fetching universities, fetching country coordinates, and computing haversine distance, and one public `predict` method that orchestrates the full pipeline. The two Flask routes in `modelrec_routes.py` call `predict` and return JSON:

- `GET /modelrec/predict/<budget>/<degree>/<size>` — returns top 10 matches, shown on the main student portal
- `GET /modelrec/predict/all/<budget>/<degree>/<size>` — returns top 100 matches, shown when the student clicks "see more".

### The Data Pipeline

The model data went through three major iterations across the phases. In Phase 2 it used raw `student_fees` which was the total institutional fee revenue in the millions — completely incomparable to what a student would actually pay. In Phase 3 this was replaced with `per_student_fees` computed by dividing total fee revenue by total enrolled students from a separate ETER enrollment dataset downloaded from the ETER database. In Phase 4 the `REV.TUITFEES` flag from the dataset was incorporated (0 = no fees, 1 = partial fees, 2 = fees for all students) so universities that charge no fees get `per_student_fees = 0` and always pass the budget filter rather than being treated as missing data.

The final `modelrec` table has 780 EU universities with columns for name, city, student fees, charges fees flag, per-student fees, highest degree, staff FTE, web pages, total students, latitude, and longitude. A separate `country_coords` table stores the coordinates for all 27 EU countries.

### Budget Filter

Before Phase 4 the budget input was only used as a cosine similarity feature, meaning universities with fees far above the student's budget could still rank highly if the other features matched. A hard filter was added before cosine similarity runs that removes any university where `per_student_fees` exceeds the student's budget. Universities with zero fees always pass the filter. If the student opts out of budget filtering the filter is skipped and all universities are ranked.

### Distance Filter — Haversine

The distance filter was the most technically significant change this phase. The original implementation used geopy, a Python library that calls the Nominatim geocoding API to convert city names to coordinates and compute distances. The problem was that every request was making 418 live HTTP calls to an external API — one per university city — causing timeouts that made the feature completely unusable.

The fix was to pre-compute and store all coordinates. A geocoding script was run once to get the latitude and longitude for every university city and every EU country and saved to the database. The `university` table had `latitude` and `longitude` columns added, and a new `country_coords` table was created.

The distance calculation itself was replaced with the haversine formula.

This runs in microseconds per university with no external calls. The student selects their home country from a dropdown of all 27 EU countries, the model looks up that country's coordinates from `country_coords`, then computes the haversine distance to each university's stored coordinates and filters out any that exceed the student's maximum distance preference before running cosine similarity on the remaining set.

## EU Labor Forecasting & Budget Reallocation Models

### What the Models Do

We built two connected machine learning features. The first forecasts EU labor demand; the second uses that forecast to recommend how a university should reallocate its program budget. They stack together as the budget model is a consumer of the labor model's output.

Labor forecasting is built from linear regression models trained on Eurostat employment data, broken out by country (`geo`) and industry sector (NACE code).

- **Model 1 (level)** predicts next year's employment in a sector from a single feature: last year's employment. It answers "given where this sector is now, where will it be next year?"
- **Model 2 (change)** predicts the year-over-year *change* in employment from three features: the number of graduates entering the sector, last year's employment, and the year.

The two are combined into one forecast, weighting each model by how accurate it is.

**Budget reallocation** is a demand-scoring model. It takes the body of students at a university, maps each student's major to the industry sectors that major feeds into, and recommends shifting funding toward the programs whose sectors the labor data says are heating up in the near future using our predictor model for the labor statistican. For each program it returns a status: Underfunded, Overfunded, or Balanced and a  budget adjustment.

### Software

The model logic lives in three modules inside the Flask backend separated from the routes that serve them:

- `backend/ml_models/labor.py`: the two regression models and the ensemble. The trained coefficients, intercepts, and `StandardScaler` parameters are stored directly as NumPy arrays in this file, so prediction at request time is  arithmetic. There is no sklearn dependency and no model file to load at runtime.
- `backend/ml_models/budget.py`: sector demand scoring and the reallocation logic.
- `backend/ml_models/crosswalk.py`: the major-to-sector mapping and the logic that turns a list of students into program weights.

These are served through two Flask blueprints:

- `labor_routes.py` provides `GET /labor/predict/level/<emp_lag1>`, `GET /labor/predict/change/<graduates>/<emp_lag1>/<time>`, and full CRUD on `/labor/observations`. The observations route also computes the forecast per row before returning it, so the charts page receives predictions already merged.
- `budget_routes.py` provides the reallocation endpoint, following the same `get_db()` and dict-cursor pattern as the rest of the backend.

On the frontend, two Streamlit personas consume these: a **labor statistician** view with an interactive prediction tool and Plotly dashboards (including a custom scatter), and a **budget manager** view that turns the reallocation output into a funding plan.

### The Data Pipeline

The labor data is Eurostat EU27 employment, 2013–2023, joined to graduate counts through an **ISCED-NACE crosswalk**. ISCED is the international classification for fields of education; NACE is the EU classification for industry sectors. The crosswalk is the connector: it maps a student's field of study to the industry sectors that field actually feeds. Computer Science (ISCED 06) maps to NACE J (ICT) and professional services; Engineering (ISCED 07) to C (manufacturing), F (construction), and others; Business (ISCED 04) to K (finance) and the professional-services group, and so on.

From the raw employment and graduate figures I derived the columns the models and dashboards use: `emp_change` (year-over-year change), `absorption_rate` (employment change per prior-year graduate, i.e. how many new jobs a sector creates relative to the graduates it produced), and `grad_ratio`. These live in the `labor_observations` table alongside `geo`, `time`, `nace_r2`, `sector`, `employment_thousands`, and `graduates`.

The budget side reads from the same `labor_observations` table and writes plans to a `budget_plan` table keyed to a `university_id` and a `budget_manager_id`.

### Model Assumptions & Predictive Checks

**Both models are OLS regressions, so they carry the standard OLS assumptions:** a linear relationship between predictors and target, independent errors, constant error variance (homoscedasticity), roughly normal residuals, and no severe multicollinearity. Features were standardized with `StandardScaler` before fitting, and the exact scaler means and scales are stored and reapplied at inference so a request is scaled identically to the training data.

**Model 1 scores R^2 = 0.99 but that number is a caveat.** The single feature is last year's employment, and employment is extremely persistent: a sector with 700k workers this year almost certainly has roughly 700k next year. The model is, in effect, learning "next year ≈ this year." The fit is correct but the target is easy, and the high R^2 partly reflects autocorrelation rather than any predictive insight.

**Model 2 scores R^2 = 0.21, and that low number is the realistic one.** Predicting the *change* in employment is a much harder problem than predicting the level, and the model only explains about a fifth of the variance. Worth noting from the coefficients: after scaling, prior employment carries by far the largest weight (≈13.2), while the graduate count, the variable the entire crosswalk exists to produce has almost no predictive weight (≈−0.88). So the data is telling us graduate inflow is a weak short-term predictor of employment change, which is a finding in itself.

**The ensemble weighting is itself a predictive check.** Rather than averaging the two models equally, the merge function weights each by its own R²: Model 1 gets 0.99 / (0.99 + 0.21) ≈ **0.825** and Model 2 gets ≈ **0.175**. The model that validated poorly is automatically trusted less.

**The assumption most at risk is independence of errors.** The data is panel-shaped: the same country–sector pair is observed repeatedly across years, and those observations are serially correlated. Plain OLS treats them as independent, which understates the true standard errors. We did not fit panel or fixed-effects models, so the confidence in any single coefficient should be read with that limitation in mind.

### What I'd Verify Before Trusting It Further

The R^2 values are validation, but they aren't the whole. Before relying on these in anything beyond a prototype I'd add: residual-vs-fitted plots to test linearity and homoscedasticity, a held-out test split or time-aware cross-validation (rather than scoring on the training data), and a variance-inflation check for multicollinearity in Model 2. The change model in particular needs better features, its 0.21 R^2 says the three current inputs aren't enough.