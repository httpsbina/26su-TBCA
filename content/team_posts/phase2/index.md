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

# ER Diagrams

## User Personal - Student
![Alt Text](phase-2-er-student.jpeg)

## User Personal - Labor
![Alt Text](phase-2-er-labor.jpeg)

## User Personal - Budget
![Alt Text](phase-2-er-budget.jpeg)

## User Personal - Global
![Alt Text](phase-2-er-global.jpeg)

# Relational Models

## User Personal - Student
![Alt Text](phase-2-relational-model-student.png)

## User Personal - Labor
![Alt Text](phase-2-relational-model-labor.png)

## User Personal - Budget
![Alt Text](phase-2-relational-model-budget.png)

## User Personal - Global
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