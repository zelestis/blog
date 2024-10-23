---
title: Benchmarking JIT Databases
description: 
permalink: honours_benchmarks
aliases: 
tags: 
draft: false
---
The essentials that I want to benchmark are LingoDB, PostgreSQL and LingoDB. I was given a test server by the university, but there's a critical issue. I don't have sudo access, and 99.95% of the work to do these benchmarks needs me to have sudo. It's literal hours to install the packages needed for benchmarking, and when I do install things it affects the entire system (new DB ports and so on).

My supervisor recommended running TPC H with a scale factor of 1, then 10