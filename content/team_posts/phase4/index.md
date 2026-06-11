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
