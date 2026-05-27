---
title: "Alyssa Haidar"
date: 2026-05-17
draft: false
description: "Individual Deliverables"
slug: "blog2"   # if you use, needs to be different for every post
tags: ["authors", "config", "docs"]
authors:
  - "alyssa_haidar"
showAuthorsBadges : false
---

# Phase 2 Overview

## University Ranking Model For Students

For Phase 2 my main focus on the data science side was on the student persona, building the ranking model that would allow students to answer survey questions and get a resulting ranking of the top universities best fit for them personally. I started by pulling university data from the Hipo University Domains API, filtering it down to only the 27 EU member states, and saving it as a csv so we would not have to repeatedly call the API every time we reran the notebook cells. The bigger challenge was working with the ETER university budget statistics dataset. This was because it came as an Excel file where all columns were stored as one long semicolon separated string per row instead of a normal table with values in separate organized cells. I wrote a Python script to load the excel file, split each row on the semicolons, assign the first row as column headers, and clean up the missing values displayed as 'm' and replacing them with NA. After renaming the columns to more "user friendly" names and selecting the relevant features, I merged the two datasets together on the English university name column from the ETER data. Getting the two sources to match was a bit of a challenge given encoding differences in European university names with special characters. With the help of my TA we were able to fix the momentary issue to allow for most universities to have user friendly names. Unfortunately a few universities are left with names that include special characters, however that will be addressed in phase 3 as for phase 2 it wasn't a core issue to attack at the time.

## This Past Week...
### The Speaker of the Week.
I have enjoyed this past week in the program with visiting brussels and hearing more interesting guest speakers, my favorite of which is the speaker who works in a think tank. He was honest in his responses and not always positive which I think made the discussions with him even more engaging. Having the honest ideas, reactions, and an overall honest opinion disussion allowed me to ask more interesting questions beyond the surface level. It was refreshing and I think he was my overall favorite of the speakers so far.

### My Day Off!
On the day off we had on sunday I had the unique opportunity to go visit Bruges with Elise, Meghan, Bina, and Minju. It was such a beautiful town with breathtaking architecture. We spent the day walking all over the city, taking a lot of pictures, going to a street market where I got some cute magnets for my collection, and just enjoying the weather.

