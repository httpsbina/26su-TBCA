---
title: "Blog Post #3"
date: 2026-05-17
draft: false
description: "Individual Deliverables"
slug: "blog2"   # if you use, needs to be different for every post
tags: ["authors", "config", "docs"]
authors:
  - "alyssa_haidar"
showAuthorsBadges : false
---

# Phase 3 Overview

This phase my main focus was taking the university ranking model I built in 
phase 2 and getting it out of the Jupyter Notebook and into the actual app.

On the model side, I converted the notebook into a `university_ranking_model` 
Python class in `modelrec.py` that pulls university data directly from the 
database at prediction time instead of from a csv file. I also converted the 
cleaned model dataset into a SQL file (`02_modelrec.sql`) and loaded it into 
the `modelrec` table in the database so the model has data to work with.

I also updated the campus size formula since we changed the survey question 
from a 1-10 slider to Small, Medium, Large options. The formula previously 
mapped size on a 1-10 scale and I updated it to use 1-3 instead.

For the routes I wrote two endpoints in `modelrec_routes.py`:
- `GET /modelrec/predict/<budget>/<degree>/<size>` - returns top 10 matches
- `GET /modelrec/predict/all/<budget>/<degree>/<size>` - returns all ranked 
universities

On the frontend I am currently working on the student portal page 
(`02_Student_Data.py`) which shows personalized recommendations pulled live 
from the ML model route and the student's saved favorites. The survey page 
feeds into this page by passing the student's budget, degree level, and 
campus size through `st.session_state`. These pages are still in progress 
and being debugged.

## This Past Week...
### The Speaker of the Week.
I dont think I have a favorite this week. I personally loved the speakers we heard on 6/3 at the think tank who gave a new perspective on the issues we have heard from their counterpart think tanks and other EU insitutions. I loved the guest speaker they brought in specifically who wasn't making a perfect future narrative but was honest and real in her answers. I think I've definately said that about my previous favorite speakers. However this is because the speakers such as them are passionate but honest and not trying to paint a pretty little picture of what the world or EU is, not making them morally superior, just being honest in the state of the world, and wanting to open discussions with us rather than talk at us.

### My Evening and Day Off in France!
So while we were in strasboug on saturday evening I had a great time shopping till I dropped with Meghan, Elise, and Minju. The next day Meghan and I took a train to the town of Nancy, which had a lovely main square, horrible lattes, but peacocks and goats in a minature zoo in their main park. In the evening we took a train to Colmar to meet with Minju and get dinner by the canals. The town was my favorite of the two because it was so beautiful and something straight out of a movie. Apparently the town inspired Bells town in Beauty and the Beast.

* P.S. Currently cannot update with photos but will for phase 4.

