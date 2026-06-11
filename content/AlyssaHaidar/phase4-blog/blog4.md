---
title: "Blog Post #4"
date: 2026-05-17
draft: false
description: "Individual Deliverables"
slug: "blog2"   # if you use, needs to be different for every post
tags: ["authors", "config", "docs"]
authors:
  - "alyssa_haidar"
showAuthorsBadges : false
---

---
title: "Blog Post #5"
date: 2026-06-10
draft: false
description: "Individual Deliverables"
slug: "blog5"
tags: ["authors", "config", "docs"]
authors:
  - "alyssa_haidar"
showAuthorsBadges : false
---

# Phase 4 Overview

This phase my focus was finalizing the university ranking model and improving how it handles student preferences.

The biggest change was replacing the live geopy geocoding with a pre-computed coordinate approach using the haversine distance formula. Previously the distance filter was calling an external geocoding API for every university on every request, which was too slow for a web app. I saved coordinates for all universities and all 27 EU countries to the database, so now the distance calculation uses haversine using the precomputed coordinate values.

I also fixed how student budget is handled in the model. Before, the budget input wasn't actually filtering universities, it was just being used as a cosine similarity feature. Now the model first filters out any university where the estimated per-student fee exceeds the student's budget, then runs cosine similarity on the remaining set. I also updated the `per_student_fees` calculation to account for whether a university actually charges fees using the `REV.TUITFEES` flag (0 = no fees, 1 = partial fees, 2 = fees for all students), so universities that are free always pass the budget filter.

Additionally, I replaced the raw `student_fees` institutional total with `per_student_fees`, which divides total fee revenue by total enrolled students from a separate ETER enrollment dataset. This makes the budget comparison meaningful since a student's budget is a personal number and the old feature was an institutional total in the millions.

For the frontend I updated the survey to replace the country text input with a dropdown of all 27 EU countries so it matches the coordinate lookup table exactly. I also simplified the distance slider to a single value instead of a range since only the maximum distance is used by the model.

## This Past Week...

### Final Week in Brussels
It's hard to believe this is the last week. Brussels has grown on me more than I expected. The food, the architecture, and honestly just constantly exploring new towns and cities was so fun. I think that the experience went by so quickly because there always was something to do or somewhere to go or something to see. I'm going to miss it but ultimately think its been an amazing 5 weeks and I am ready to go back home, see family and my dogs.