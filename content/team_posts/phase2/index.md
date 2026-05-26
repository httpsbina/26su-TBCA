---
title: "Project - Phase II"
date: 2026-05-25
draft: false
description: "Our Idea"
slug: "phase2post"
tags: ["project", "Setup"]
authors:
  - "alyssa_haidar"
  - "bina"
  - "charles_chapman"
  - "tyler_gladu"
showAuthorsBadges: false
---

# ER Diagrams and Relational Models

## User Personal - Student
![Alt Text](phase-2-er-student.jpeg)
![Alt Text](phase-2-relational-model-student.png)

### Description
When students create their account they are asked for this basic information (name, email, major, address). This is then stored in the database and used throughout the app such as by informing the *Labor Statistician* persona about trending majors. While the student interacts with the app, they are able to favorite universities which then informs the *Budget Manager* persona about trending universities. Students are also able to view university statistics and make pros/cons lists for each university.

### Relationships and Cardinalities
Students are only able to fill out one survey and edit responses later. They are able to favorite any university as well as create pros/cons lists for any university. Universities can have multiple academic reports for each year, but academic reports can only be associated with one university. Survey responses are a weak entity because they are reliant on the student entity. Academic reports are a weak entity because they are reliant on the university entity. Favorites and Pros/Cons are associative entities because they represent a multiple-to-multiple relationship between students and universities.

## User Personal - Labor
![Alt Text](phase-2-er-labor.jpeg)
![Alt Text](phase-2-relational-model-labor.png)

### Description
Labor statisticians are able to view statistics and data about academic performance, as well as seeing which majors are trending to inform potential implications for the labor sector.

### Relationships and Cardinalities
Universities are able to be liked by any user. The relationship between universities and academic reports is described above.

## User Personal - Budget
![Alt Text](phase-2-er-budget.jpeg)
![Alt Text](phase-2-relational-model-budget.png)

### Description
Budget managers are able to view statistics and data related to historical academic reports as well as historical budgets for each university. They are also able to create budget plans for groups of universities and simulate how they will perform based on historical data.

### Relationships and Cardinalities
Budget workers are able to create multiple budget plans. Each budget plan can have multiple university budget plans included within them. University budget plans are linked with a singular university. Historical university budgets are linked to a singular university, while any university can have multiple historical budgets. Budget plans are a weak entity because they are reliant on the government worker that created them. Budgets are a weak entity because they are reliant on the university that it is related to. University budget plans are an associative entity because they represent a many-to-many relationship between budget plans and universities.

## User Personal - Global
![Alt Text](phase-2-er-global.jpeg)
![Alt Text](phase-2-relational-model-global.png)

# Global DDL

```sql
CREATE TABLE student (
    id          INTEGER PRIMARY KEY,
    name        VARCHAR(100) NOT NULL,
    email       VARCHAR(100) NOT NULL,
    address     VARCHAR(255),
    major       VARCHAR(100)
);

CREATE TABLE university (
    id          INTEGER PRIMARY KEY,
    name        VARCHAR(100) NOT NULL,
    location    VARCHAR(255)
);

CREATE TABLE gov_worker (
    id          INTEGER PRIMARY KEY,
    name        VARCHAR(100) NOT NULL
);

CREATE TABLE survery_form (
    student_id  INTEGER NOT NULL,
    created_at  DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at  DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (student_id) REFERENCES student(id)
);

CREATE TABLE favorites (
    student_id    INTEGER NOT NULL,
    university_id INTEGER NOT NULL,
    created_at    DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (student_id)    REFERENCES student(id),
    FOREIGN KEY (university_id) REFERENCES university(id)
);

CREATE TABLE pros_cons (
    created_at    DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at    DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    pros          VARCHAR(10000),
    cons          VARCHAR(10000),
    student_id    INTEGER NOT NULL,
    university_id INTEGER NOT NULL,
    FOREIGN KEY (student_id)    REFERENCES student(id),
    FOREIGN KEY (university_id) REFERENCES university(id)
);

CREATE TABLE academic_reports (
    university_id   INTEGER NOT NULL,
    year            YEAR NOT NULL,
    students        INTEGER,
    graduation_rate FLOAT,
    avg_gpa         FLOAT,
    FOREIGN KEY (university_id) REFERENCES university(id)
);

CREATE TABLE budget_plan (
    id              INTEGER PRIMARY KEY,
    university_id   INTEGER NOT NULL,
    worker_id       INTEGER NOT NULL,
    created_at      DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at      DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    total_amount    INTEGER,
    FOREIGN KEY (university_id) REFERENCES university(id),
    FOREIGN KEY (worker_id)     REFERENCES gov_worker(id)
);

CREATE TABLE budget_plan_university (
    plan_id     INTEGER NOT NULL,
    created_at  DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at  DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    amount      INTEGER,
    FOREIGN KEY (plan_id) REFERENCES budget_plan(id)
);
```

# Wireframes
### Description
We used "Good Notes" to illustrate our wireframes for our application. This allowed us to quickly and efficiently outline page layouts and create key UI components for each user persona. We made three seperate portal designs, one for the Student, Labor Statistician, and Budget Manager. Each portal contains multiple interactive pages that provide the user a clean workflow. The sketches include elements such as data tables and charts to give a visual reference for how the application should look and behave once implemented.
![Alt Text](Wireframes-TBCA-Final-1.jpg)
![Alt Text](Wireframes-TBCA-Final-2.jpg)
![Alt Text](Wireframes-TBCA-Final-3.jpg)
![Alt Text](Wireframes-TBCA-Final-4.jpg)
![Alt Text](Wireframes-TBCA-Final-5.jpg)
![Alt Text](Wireframes-TBCA-Final-6.jpg)
![Alt Text](Wireframes-TBCA-Final-7.jpg)
![Alt Text](Wireframes-TBCA-Final-8.jpg)
![Alt Text](Wireframes-TBCA-Final-9.jpg)
