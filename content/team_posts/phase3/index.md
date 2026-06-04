---
title: "Project - Phase III"
date: 2026-06-03
draft: false
description: "Continuing our project"
slug: "phase3post"
tags: ["project", "Setup"]
authors:
  - "alyssa_haidar"
  - "bina"
  - "charles_chapman"
  - "tyler_gladu"
showAuthorsBadges: false
---
## Updates Since Phase 2

### Updates to Data Model
Since phase 2, we have added minor updates to our data model:
- **Add labor_statistician entity:** Due to the requirements to store mock data about all three user personas, we added a simple labor_statistician entity. This entity contains an id, first_name, last_name, and email. This will allow us to add functionality to save stats, and other features.
- **Separate name attributes into first_name and last_name:** We realized that different parts of our site may only need the first name, so rather than splitting the name attribute, we decided it would be easier to simply store the first and last name separately.

## Routes

### REST API Matrix
To start planning our REST API, we used the functionality planned for in our wireframes to devise a list of routes needed in our app. Then listed these in this REST API Matrix along with the scenarios in which these routes will be used within the app.

{{< csvtable file="routes.csv" >}}

#### Route Syntax Details
In creating our routes, we considered how to label the routes to make them the most understandable for those working with them. This included labeling them first by what was being access ('universities' and 'students'), and then narrowing to more specific info (such as specific stats). When specific entries had to be accessed, such as a specific student or university, we would simply include their id in the route (.../{id}). This differs from the syntax used when creating filters to filter out specific entries that had the same value such as stats about a specific university. This was done by including query parameters(...?id={id}).

### Implementing Routes
After theorizing routes in the REST API Matrix, we implemented them in python in our project's backend. This involved following the format provided in the syntax (cursor commands, try/except to catch errors, etc) and theorizing how the frontend would interact with the API and how the frontend would need data to be presented. One specific example of this was the route for a student to favorite/unfavorite a university. In our UI, this would likely look like a "like" button that would be toggled on and off. Hence, rather than splitting the function ran when the button is selected into three routes (check if university is favorited, if it isn't POST a new relation row, if it is DELETE that relation row), we decided it would be easier to call a single route and have the API do the work under the hood. Hence, when the frontend calls *POST /favorites/{student_id}/{university_id}*, the API does all this under the hood. This route looks as follows:

```python
@university_explorer_routes.route(
    "/favorites/<int:student_id>/<int:university_id>", methods=["POST"])
def toggle_favorite(student_id, university_id):
    """Toggle a favorite for a student
    If the favorited row exists in the table it deletes it.
    If the favorited row does not exist in the table it creates it.
    """
    current_app.logger.info(f"POST /favorites/{student_id}/{university_id}")
    try:
        db = get_db()
        cursor = db.cursor()
        # First queries for the favorite to see whether it already exists
        cursor.execute(
            """SELECT 1
                 FROM favorites
                WHERE student_id = %s AND university_id = %s""",
            (student_id, university_id),
        )
        exists = cursor.fetchone() is not None

        if exists:
            # If the row exists, remove it
            cursor.execute(
                """DELETE FROM favorites
                    WHERE student_id = %s AND university_id = %s""",
                (student_id, university_id),
            )
            db.commit()
            return jsonify({"favorited": False}), 200

        # If the row doesn't exist, add it
        cursor.execute(
            "INSERT INTO favorites (student_id, university_id) VALUES (%s, %s)",
            (student_id, university_id),
        )
        db.commit()
        return jsonify({"favorited": True}), 200
    except Error as e:
        current_app.logger.error(f"POST favorite toggle failed: {e}")
        return error_response("Could not toggle favorite", 400)
```

## Data Model Updates
 
We added the following tables since Phase II:
 
- labor_observations: (**sourced**) 2,354 rows of cleaned Eurostat data (employment by sector, graduates by field, working-age population) across EU27, 2013–2023
- model1_params: (**generated**) trained weights for the employment level prediction model
- model2_params: (**generated**) trained weights for the employment change prediction model
- labor_statistician: (**generated**) mock user data for the labor statistician persona

## ML Features
 
**Model 1 — Employment Level**
Feature: last year's employment (employment_thousands_lag1). Employment is highly autocorrelated year over year, making the lag the strongest single predictor.
 
**Model 2 — Employment Change**
Features: graduates entering the sector, last year's employment, and year. Graduates measure supply-side pipeline pressure, the lag has the sector size and momentum, and year gets the long-run trends like ICT growth and agricultural decline across the EU.
 
## Surprising Issues
 
- Model 1 performed poorly when using only graduate counts, adding the employment lag resolved this, since graduates alone don't tell you how large a sector already is
- Model 2 R^2 was modest (~0.36), which makes sense: employment change is pushed by factors outside our data like recessions, policy, and COVID-19
- The 2020 data year is a visible outlier in several sectors due to COVID, which affected test performance
- Mapping Eurostat's graduate fields (ISCED) to employment sectors (NACE) required a manual crosswalk, an approximation that limits model precision

## REST API Matrix
 
| Method | Route | Description | User Story |
|--------|-------|-------------|------------|
| GET | `/labor/predict/level/<emp_lag1>` | Predict employment level | Statistician forecasts sector employment |
| GET | `/labor/predict/change/<graduates>/<emp_lag1>/<time>` | Predict employment change | Statistician checks if grad supply matches demand |
| GET | `/labor/observations` | All labor observations | Populate charts |
| GET | `/labor/observations/<geo>` | Filter by country | Specific country |
| GET | `/labor/sectors` | List sectors | Populate UI dropdowns |
| GET | `/labor/countries` | List countries | Populate UI dropdowns |
| GET | `/labor/absorption` | Absorption rate by sector | Show graduate surplus/deficit by sector |
| POST | `/labor/observations` | Add a data point | Admin adds new Eurostat data |
| PUT | `/labor/observations/<id>` | Update a data point | Admin corrects an error |
| DELETE | `/labor/observations/<id>` | Remove a data point | Admin removes a flagged row |