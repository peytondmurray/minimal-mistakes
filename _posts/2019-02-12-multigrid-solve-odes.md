---
title: Solving Linear ODEs With Multigrid
layout: single
author_profile: true
read_time: true
share: true
date: '2019-02-26 14:30:00 -0800'
categories: coding
toc: true
---

# Note

This is a work in progress. It's going to take a few days to write everything out and get it looking nice. I'll remove this note when I'm done.

# Introduction

About a year ago, I was working on a for-fun problem where I needed to determine the voltage from an arbitrary charge distribution along 1 dimension. This is the problem of solving a linear differential equation, in this case, Poisson's equation, given a driving function $$F$$:

$$
\nabla^2 v = F\label{eq:one}
$$

Unless we're involved in developing these sorts of libraries regularly, it's not too often that we think about how these problems can be solved numerically, with the fact there aren't very many blog posts about this kind of stuff a good testament to how infrequently we think about these sorts of algorithms (It's also just hard to write this kind of material without it being mind-numbingly dry. Hopefully this will be an interesting overview without getting too bogged down).

Before I get into it, I should also mention that there are a number of different commercial software options for solving these types of equations, e.g. COMSOL multiphysics, but I don't like using proprietary software if I can avoid it - you can never look under the hood and see how it works. I looked at free software libraries like FENICS, but at the time I couldn't tell whether that project was abandoned or not, so I decided to write my own solver. At the time, I knew one crude way of doing this (Jacobi iteration, which I'll get to later) that I learned back in undergrad. But after spending a few minutes waiting for my Jacobi solver to finish with a simple 1D problem, my patience wore out and I decided to look at other options. One of these approaches - multigrid - is so cool that I had to share it here.

# Defining the Problem

I have a grid of equally spaced points with a charge distribution $$\rho$$ defined at each point:

![problem_layout]

and so on. The goal is to find the voltage $v$ everywhere, where the voltage is governed by Poisson's equation, mentioned above. The context of electromagnetism (where it's most commonly encountered) it is written

$$
\nabla^2 v = -\frac{\rho}{\epsilon}
\label{eq:ref1}
$$

but for our purposes, I'm just going to use $F\equiv-\frac{\rho}{\epsilon}$. Since we're interested in solving for the value of $v$ on a set of discrete points, the first step in tackling the problem is to change the laplacian, which acts on a _continuous_ scalar field, into a _discrete_ operator. Using central finite differences scheme, a derivative can be approximated by

$$
\frac{\mathrm{d}v}{\mathrm{d}x} \approx \frac{v(x+\frac{1}{2}\Delta x) - v(x-\frac{1}{2}\Delta x)}{\Delta x}
$$

The 1D laplacian can then be found pretty easily by applying this definition twice:

$$
\nabla^2 v = \frac{\mathrm{d}}{\mathrm{d}x} \frac{\mathrm{d}v}{\mathrm{d}x} \approx \frac{\frac{\mathrm{d}v}{\mathrm{d}x}(x+\frac{1}{2}\Delta x) - \frac{\mathrm{d}v}{\mathrm{d}x}(x-\frac{1}{2}\Delta x)}{\Delta x} = \frac{v(x+\Delta x) - 2v(x) + v(x-\Delta x)}{\Delta x^2}
$$

Since it's annoying to write $v(x+\Delta x)$ everywhere, I'm just going to use the notation that $v_i$ for the value of the voltage at $x_i$, which means that $v(x+\Delta x) \rightarrow v_{i+1}$, etc. Now the discrete laplacian is just

$$
\nabla^2 v \,\,\rightarrow\,\, \frac{v_{i+1} - 2v_i + v_{i-1}}{\Delta x^2}
$$

Rewriting this in terms of matrices allows us to reduce the amount of notation even further, making $\ref{eq:ref1}$:

$$
\begin{pmatrix} 1 & -2 & 1 & \, & \, & \, & \, & \, \\ & \, 1 & -2 & 1 & \, & \, & \, & \, \\ \, & \, & \, & \, & \ddots \, & \, & \, & \\ \, & \, & \, & \, & \, & 1 & -2 & 1 \end{pmatrix}
\begin{pmatrix} v_1 \\ v_2 \\ \vdots \\ v_{n-1} \end{pmatrix}
= \begin{pmatrix} F_1 \\ F_2 \\ \vdots \\ F_{n-1} \end{pmatrix}
$$


# Jacobi Iteration

[problem_layout]: /assets/images/multigrid/problem_layout.png
{: .align-center}