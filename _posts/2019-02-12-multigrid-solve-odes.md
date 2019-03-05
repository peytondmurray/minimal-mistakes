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
\begin{align}
\nabla^2 v = F
\label{eq:ref1}
\end{align}
$$

Unless we're involved in developing these sorts of libraries regularly, it's not too often that we think about how these problems can be solved numerically, with the fact there aren't very many blog posts about this kind of stuff a good testament to how infrequently we think about these sorts of algorithms (It's also just hard to write this kind of material without it being mind-numbingly dry. Hopefully this will be an interesting overview without getting too bogged down).

Before I get into it, I should also mention that there are a number of different commercial software options for solving these types of equations, e.g. COMSOL multiphysics, but I don't like using proprietary software if I can avoid it - you can never look under the hood and see how it works. I looked at free software libraries like FENICS, but at the time I couldn't tell whether that project was abandoned or not, so I decided to write my own solver. At the time, I knew one crude way of doing this (Jacobi iteration, which I'll get to later) that I learned back in undergrad. But after spending a few minutes waiting for my Jacobi solver to finish with a simple 1D problem, my patience wore out and I decided to look at other options. One of these approaches - multigrid - is so cool that I had to share it here.

# Defining the Problem

I have a grid of equally spaced points with a charge distribution $$\rho$$ defined at each point:

![problem_layout]

and so on. The goal is to find the voltage $v$ everywhere, where the voltage is determined by Poisson's equation, mentioned above. In the context of electromagnetism (where it's often encountered) it is written

$$
\nabla^2 v = -\frac{\rho}{\epsilon}
$$

Since we're interested in solving for the value of $v$ on a set of discrete points, the first step in tackling the problem is to change the laplacian, which acts on a _continuous_ scalar field, into a _discrete_ operator. Using central finite differences scheme, a derivative can be approximated by

$$
\frac{\mathrm{d}v}{\mathrm{d}x} = v' \approx \frac{v(x+\frac{1}{2}\Delta x) - v(x-\frac{1}{2}\Delta x)}{\Delta x}
$$

The 1D laplacian can then be found pretty easily by applying this definition twice:

$$
\nabla^2 v = \frac{\mathrm{d}v'}{\mathrm{d}x} \approx \frac{v'(x+\frac{1}{2}\Delta x) - v'(x-\frac{1}{2}\Delta x)}{\Delta x} = \frac{v(x+\Delta x) - 2v(x) + v(x-\Delta x)}{\Delta x^2}
$$

Since it's annoying to write $v(x+\Delta x)$ everywhere, I'm just going to use $v_i$ for the value of the voltage at $x_i$, which means that $v(x+\Delta x) \rightarrow v_{i+1}$, etc. Now the discrete laplacian is just

$$
\nabla^2 v \,\,\rightarrow\,\, \frac{v_{i+1} - 2v_i + v_{i-1}}{\Delta x^2}
$$

Rewriting this in terms of matrices allows us to reduce the amount of notation even further, making $(\ref{eq:ref1})$:

$$
\begin{align}
\begin{pmatrix} 1 & -2 & 1 & \, & \, & \, & \, & \, \\ & \, 1 & -2 & 1 & \, & \, & \, & \, \\ \, & \, & \, & \, & \ddots \, & \, & \, & \\ \, & \, & \, & \, & \, & 1 & -2 & 1 \end{pmatrix}
\begin{pmatrix} v_0 \\ v_1 \\ \vdots \\ v_{n} \end{pmatrix}
= \begin{pmatrix} F_0 \\ F_1 \\ \vdots \\ F_{n} \end{pmatrix}
\label{eq:ref2}
\end{align}
$$

where $F_i\equiv-\Delta x^2 \rho_i/\epsilon$. Things get [a little more complicated](#appendix-dirichlet-boundary-conditions) if you want to find out what happens if we apply some voltage at the boundary, but I've left the details in the extra section at the bottom, an the math that follows still applies.


# Jacobi Iteration


# Appendix: Dirichlet Boundary Conditions

To include the effects of fixed voltage at the boundary points $v_0$ and $v_n$ into $(\ref{eq:ref2})$, we only need to write down the discrete Poisson's equation at the adjacent points:

$$
\begin{align}
v_0 - 2v_1 + v_2 = F_1 \,\,\,\, &\rightarrow \,\,\,\, F_1 - v_0 = 2v_1 + v_2 \\
v_{n-2} - 2v_{n-1} + v_n = F_{n-1} \,\,\,\, &\rightarrow \,\,\,\, F_{n-1} - v_{n} = v_{n-2} - 2v_{n-1}
\end{align}
$$


[problem_layout]: /assets/images/multigrid/problem_layout.png

{: .align-center}