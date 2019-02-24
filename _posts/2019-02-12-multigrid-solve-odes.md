---
title: Solving Linear ODEs With Multigrid
layout: single
author_profile: true
read_time: true
share: true
date: '2019-09-12 14:30:00 -0800'
categories: coding
---

About a year ago, I was working on a for-fun problem where I needed to determine the electric field from an arbitrary
distribution of charge along 1 dimension. At the time, I knew one crude way of doing this (Jacobi iteration,
which I'll get to later), that I learned back in undergrad. There are a number of ways that this problem can be solved,
and one of these approaches - multigrid - is so cool that I had to share it here.

So the basic idea is this.