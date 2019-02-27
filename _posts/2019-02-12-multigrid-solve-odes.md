---
title: Solving Linear ODEs With Multigrid
layout: single
author_profile: true
read_time: true
share: true
date: '2019-02-30 14:30:00 -0800'
categories: coding
toc: true
---


# Introduction

About a year ago, I was working on a for-fun problem where I needed to determine the voltage from an arbitrary charge distribution along 1 dimension. This is the problem of solving a linear differential equation, in this case, Poisson's equation, given a driving function $$F$$:

$$
\begin{equation}
\nabla^2 v = F\label{eq:one}
\end{equation}
$$

Unless we're involved in developing these sorts of libraries regularly, it's not too often that we think about how these problems can be solved numerically, with the fact there aren't very many blog posts about this kind of stuff a good testament to how infrequently we think about these sorts of algorithms (It's also just hard to write this kind of material without it being mind-numbingly dry. Hopefully this will be an interesting overview without getting too bogged down).

Before I get into it, I should also mention that there are a number of different commercial software options for solving these types of equations, e.g. COMSOL multiphysics, but I don't like using proprietary software if I can avoid it - you can never look under the hood and see how it works. I looked at free software libraries like FENICS, but at the time I couldn't tell whether that project was abandoned or not, so I decided to write my own solver. At the time, I knew one crude way of doing this (Jacobi iteration, which I'll get to later) that I learned back in undergrad. But after spending a few minutes waiting for my Jacobi solver to finish with a simple 1D problem, my patience wore out and I decided to look at other options. One of these approaches - multigrid - is so cool that I had to share it here.

# Defining the Problem

I have a grid of equally spaced points with a charge distribution $$\rho$$ defined at each point. The goal is to find the voltage $v$ everywhere, where the voltage is governed by Poisson's equation, mentioned above. In the usual form it is found in an electromagnetism textbook,

$$
\begin{equation}
\nabla^2 v = -\frac{\rho}{\epsilon}
\end{equation}
$$

but for our purposes, I'm just going to use $F=-\frac{\rho}{\epsilon}$.

