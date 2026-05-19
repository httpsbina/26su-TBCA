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

## Cher Lemieux - Bina

Cher Lemieux is the sitting Executive Vice-President for Social Rights, Skills, Quality Jobs, and Preparedness under the European Commission. She is responsible for ensuring that European workers have access to quality employment, that education systems across member states are producing skilled graduates, and that the EU workforce remains competitive and prepared for future economic shifts. As student-to-workforce pipelines become increasingly data-driven, tools that connect secondary students to the right university programs are directly relevant to her mandate; better university matching means stronger graduate outcomes, which feeds directly into EU labor market health.

* Age: 31
* Occupation: Executive Vice-President for Social Rights, Skills, Quality Jobs, and Preparedness
* Nationality: French

### User Story 1

Cher has started noticing something interesting in the data: students across the EU are signaling where the workforce is headed long before they ever graduate through their declared/intrested majors. As Cher Lemieux, I want to see what fields students are choosing to study and compare that against where each region actually needs workers. If students in a particular country are flooding into programs the local economy has no room for, or ignoring fields it desperately needs, I want to know. I can use this tool to catch those patterns early and push funding toward the universities and programs that will matter most.

### User Story 2

The Commission just handed Cher a substantial grant with one directive: make it count. The easy move would be spreading it thin across every university, but Cher knows that helps no one. She wants to find the schools that are either starving for resources or producing strong outcomes without enough support behind them. As Cher Lemieux, I want to use student interest and enrollment data to identify which universities are being overlooked and which ones are already doing good work. I can use this tool to make sure the money goes to the places that will actually do something with it.

### User Story 3

Cher has been watching how certain member states lose their students for years. Students leave for universities abroad, build their lives there, and never come back. The countries that can least afford to lose young talent are losing the most of it. As Cher Lemieux, I want to see how many students are actively looking to study outside their home country and where they are hoping to go. I can use this tool to get ahead of it, understanding the scale of outward interest region by region so she can start working with member states to build up what is keeping students from wanting to stay in the first place.

### User Story 4

One of the biggest things holding students back from studying abroad is not the cost or the distance, it is the fear that their degree will not mean anything when they get home. Cher has heard this concern repeated across every member state and knows it is suppressing mobility numbers. As Cher Lemieux, I want to track how many students are ruling out universities in other countries and whether credential concerns are showing up as a pattern in the data. I can use this tool to build the case for pushing harder on degree standardization across the EU.

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

# Bina's Data Sources

## User Story 1: Workforce Mismatch
*Identify supply/demand gaps between student majors and labor market needs by region*

**Primary Datasets:**

- Eurostat - Students Enrolled in Tertiary Education by Field of Study (educ_uoe_enrt03)
https://ec.europa.eu/eurostat/databrowser/product/page/educ_uoe_enrt03

- CEDEFOP Skills Forecast (employment projections by occupation/sector/country)
https://www.cedefop.europa.eu/en/datasets/dataset-skills-forecast
https://www.cedefop.europa.eu/en/access-detailed-data?please_select_the_dataset_you_are_interested_in=cedsf
Note: Data request form required (free, ~2-3 days approval). Web tool currently undergoing update.

- CEDEFOP Skills Intelligence Tool (real-time skill gaps and labor market trends)
https://www.cedefop.europa.eu/en/tools/skills-intelligence

**My Plan:**
Cross-reference student major interests from the questionnaire against actual regional enrollment trends and future job demand forecasts. Flag regions where students are choosing majors that won't have jobs in 5-10 years.

## User Story 2: Funding Allocation Strategy
*Identify which universities are underfunded but producing strong outcomes*

**Primary Datasets:**

- European Tertiary Education Register (ETER) - CSV with enrollment, funding, graduation rates
https://zenodo.org/records/7871040
Direct download: https://zenodo.org/records/7871040/files/ETER_fullDump_27042023.csv?download=1

- Eurostat - Education Finance (public expenditure by institution and country)
https://ec.europa.eu/eurostat/web/education-and-training/database

- U-Multirank Open Data (university performance across multiple dimensions)
https://www.umultirank.org/
Data access: https://www.umultirank.org/about/u-multirank/data-access/

**How to Use:**
Train ML model on ETER data (funding as input, graduation/research output as target). Identify institutions that are producing good results on low budgets. Use Eurostat finance data to cross-check funding levels. U-Multirank provides additional performance signals beyond graduation rates.

## User Story 3: Brain Drain
*Track which regions are losing students and understand push factors*

**Primary Datasets:**

- UNESCO UIS - Student Mobility Statistics (outbound students by country, destination, field)
https://databrowser.uis.unesco.org/
Specific indicator: https://databrowser.uis.unesco.org/browser/EDUCATION/UIS-EducationOPRI/int-stud

- Eurostat - Learning Mobility Statistics (degree-mobile and credit-mobile flows within EU)
https://ec.europa.eu/eurostat/statistics-explained/index.php?title=Learning_mobility_statistics

**How to Use:**
Visualize outflow of students from each EU country and region. Combine with data from User Story 1 (labor market forecasts) to understand if drain is because of lack of jobs in home country or preference for studying abroad. Filter by field of study to see if certain majors have higher outflow rates.

## User Story 4: Credential Recognition Barriers
*Whether degree recognition concerns are blocking student mobility*

**Primary Datasets:**

- ENIC-NARIC Networks (qualitative: recognition procedures by country pair)
https://www.enic-naric.net/

- DEQAR API (accreditation status of institutions/programs)
https://www.eqar.eu/qa-results/search/
API documentation: https://docs.deqar.eu/stable/

- Eurostat - Education Outcomes (graduate employment rates by country and field)
https://ec.europa.eu/eurostat/web/education-and-training/database

- CIMEA - University Grading Systems Database (standardized grading scales by country)
https://www.cimea.it/EN/pagina-riconoscimento-delle-qualifiche

**How to Use:**
Use DEQAR API to flag which institutions have recognized accreditations (reduces credential anxiety). Map ENIC-NARIC recognition agreements to show students which countries recognize their home degree. CIMEA grading database provides transparency on how degrees translate across countries. Combine with graduate employment outcomes to show whether recognition barriers actually impact career prospects.