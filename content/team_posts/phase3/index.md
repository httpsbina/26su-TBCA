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

# User Interface

![Alt Text](StudentPage1.png)

*Figure 1 - Student Survey*

### Description
We created this page to allow a student user to input their personal preferences for university. Some of the options we included that are inputted into our ML model are budget, school size (Small, Medium, Large), and degree level (Bachelor's, Master's, or Doctorate's)

---

![Alt Text](StudentPage2.png)

*Figure 2 - Student Portal Page (10 Universities & 5 Favorites)*

### Description
The page above provides a student a personalized list of universities based on their preferences. Alongside it are a list of favorites that the user can choose and rank to keep track of which universities they would most like to go to.

---

![Alt Text](StudentPage3.png)

*Figure 3 - Student Portal University List (100 Universities)*

### Description
This page pulls up when a student clicks "View More" below their list of personalized universities. Our initial list only holds their top 10, but to allow students the flexibility to look at more pages, we added another page that shows their top 100 universities.

---

![Alt Text](StudentPage4.png)

*Figure 4 - Student Portal Full Favorites List (All)*

### Description
This page is similar to the previous one. When a student clicks on "View More" below their list of favorites, rather than only seeing their top 5 favorites, they're able to see all of their favorites and interact with them.

---

![Alt Text](BudgetManagerPage1.png)

*Figure 5 - Budget Manager Portal*

### Description
The page above is our intial budget manager portal. A budget manager is able to see a large amount of data including information such as flagged universities, budget plans drafted, and submission deadline for budget plans. They're also able to see a list of universities that have plans or need plans to be drafted.

---

![Alt Text](BudgetManagerPage2.png)

*Figure 6 - Budget Manager Plan View*

### Description
A budget manager is able to see a specific plan they have created for a university by clicking "View Plan" to the right of a specific university. They are provided a program reallocation table which shows data such as program name, current target, budget adjustment, and status of plan.

