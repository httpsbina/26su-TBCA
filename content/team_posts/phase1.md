---
title: "Project - Phase I"
date: 2024-05-14
draft: false
description: "Our Idea"
slug: "phase1post"
tags: ["project", "Setup"]
authors:
  - "alyssa_haidar"
  - "bina"
  - "charles_chapman"
  - "tyler_gladu"
showAuthorsBadges: false
---

# Topic Introduction - Alyssa


# Our Solution - Alyssa


# User Personas

## Student - Tyler
- Where to go to school questionnaire

## College Counselor - Bina
- School-wide data

## Zuhal Demir

- **Age:** 46
- **Occupation:** Flemish Minister for Education, Justice, and Work
- **Nationality:** Flemish

Zuhal Demir is the current sitting Flemish Minister for Educatoin, Justice, and Work. She is directly responsible for allocating funding to Flemish universities and measuring performance. In a state where Universities are almost entirely publicly funded, she has a great amount of power and influence over education in Flanders.

### User Story 1

Because students get to decide wherever in the country to study, University of Antwerp (UAntwerp) has had a declining student enrollment for many years due to its poor programs and dangerous campus. As Zuhal Demir, I want to determine whether to allocate more money to the university to make it more desirable, or reallocate funding to other more popular universities. I use this tool to predict the effects of funding amounts at UAntwerp and other universities.

### User Story 2

The Flanders Educational Department just received a large grant to fund public universities in Flanders. As Zuhal Demir, I want to examine where to allocate these funds to maximize the gain in students' academic performance. I can use this tool to gain insights into the effects of increased funding at different universities and programs.

### User Story 3

Due to budget shortages, the Flanders Educational Department must cut a large amount in funding from public universities. As Zuhal Demir, I want to examine where these cuts could occur to minimize impact on students' academic performance. I can use this tool to gain insights into the effects of decreased funding and project students' academic performance at universities based on funding received.

### User Story 4

KU Leuven just completed a large expansion and will be receiving 2000 more students next academic year. Hence, the university will require more funding to support these students. As Zuhal Demir, I want to examine how much funding would be ideal to support the students' academic performance. I can use this tool to gain insights into how students are likely to perform based on how much the school receives.


# Candidate Data Sources

[European Tertiary Education Register (ETER)](https://zenodo.org/records/7871040?preview_file=ETER_fullDump_27042023.csv)
- Downloadable CSV with hundreds of datapoints about every European tertiary education institution including enrollment, funding, graduation rates, etc.
- Could use data to train ML model for predictions based on funding, enrollment, grad rate, and other metrics.
- I (Charlie) was able to import the CSV file into a mysql database using Docker and DataGrip for exploration.