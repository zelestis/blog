---
title: Week 7 Update
description: 
permalink: week_7_update
aliases: 
tags: 
draft: false
---

* I've mostly been spending my spare time deep in a TikTok job application - I reached a very informal verbal offer! This also means more of my spare time will be dedicated to this thesis


* I decided to shift my benchmarking over to be wrapped inside a docker container just so that the research server is going to be easier to use. For a lot of these I spend *hours* installing libraries and I'd rather have it easily reproducible. It's still going to be comparable between the databases I benchmark.
* I managed to get DBT3 to benchmark PostgreSQL, but then immediately realised that the LingoDB benchmarks are a bit lackluster. The LingoDB benchmarks run within a couple of seconds, and the DBT3 ones take 3-4 hours
* Also, a raw benchmark using DBT3 or sysbench doesn't output EXPLAIN ANALYZE, which would be useful for knowing how much time is actually spent in the planning stage of the query building


* I wrote out the base structure of the slides and defined most background research pages. Obviously, I still need to put benchmarks up and a handful of background. It's only 32 slides at the moment and most of the examples they put up is 35-40 pages. 
* The main challenge isn't really making the slides anyways, it's having enough to talk about and write about for a 20-40 page document afterwards.

![[My_Thesis_A_presentation.pdf]]


* I also couldn't find the UNSW-branded slides. If you go to https://www.inside.unsw.edu.au/campus-life/download-our-new-templates-celebrating-unsws-75th-anniversary, then follow the link from there it takes you to a page not found.
* In the latest announcement, they mentioned that we can do our presentations later as long as the slides are submitted by the due date. So I'd like to propose that we schedule the presentation in early week 9, that way I'll have the weekend as extra time
* ![[Pasted image 20241024075714.png]]