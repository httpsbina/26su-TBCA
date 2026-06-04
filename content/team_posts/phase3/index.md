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
## ML Model - University Ranking Unsupervised Model
My main contribution this phase was taking the university ranking model from the Jupyter Notebook and converting it into a Python class that works with our Flask backend.

The model uses cosine similarity to rank EU universities based on three features from the student survey:
- **Student budget** - their tuition preference
- **Degree level** - Bachelors (1), Masters (2), Doctorate (3)
- **Campus size** - Small (1), Medium (2), Large (3)

These features were chosen because they directly map to what a student is looking for and we had matching data in our dataset. Staff FTE is used as a proxy for campus size since it correlates with how big the institution is. The model standardizes all features using StandardScaler, then computes cosine similarity between the student input vector and each university vector. Universities are ranked by score and returned as a dictionary keyed by rank.

### Changes from Phase 2 to Phase 3
- Converted the Jupyter Notebook into a `university_ranking_model` class in `modelrec.py`
- Updated the campus size formula from a 1-10 scale to 1-3 to match the updated survey question (Small/Medium/Large)
- Converted the cleaned model dataset (`model_df.csv`) into a SQL file (`02_modelrec.sql`) and loaded it into the `modelrec` table

### Routes
I wrote two routes in `modelrec_routes.py`:
- `GET /modelrec/predict/<budget>/<degree>/<size>` - returns top 10 matches
- `GET /modelrec/predict/all/<budget>/<degree>/<size>` - returns all ranked universities

### Frontend Pages (In Progress)
I am currently working on the student facing pages:
- `02_Student_Data.py` - the student portal showing personalized recommendations and favorites, pulling from the ML model route
- The survey page collects budget, degree level, and campus size and passes them through `st.session_state` to the portal page

### Surprising Issues
Something that took longer than expected was wiring the survey inputs into the portal page. The survey stores campus size as a string like "Small (<5,000 students)" so I had to map those back to numbers before passing them to the model. Same thing with degree level, it comes in as "Bachelor's Degree" and the model needs a 1, 2, or 3. Also the budget from the survey is just a raw number but Flask was expecting a float in the URL so I had to update the route to accept any number and cast it manually. There were a lot of small things that had to line up between the frontend and the model and every time I fixed one thing I realized I forgot to update something else connected to it. This is a surprise to me because I didn't realize how many little things and changes I miss all the time, and don't automatically think about. So that's something I also want to work on. Making sure to not forget about the little things when im so focused on the big picture.