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

About a year ago, I was working on a for-fun problem where I needed to determine the voltage from an arbitrary charge distribution along 1 dimension. This is the problem of solving a linear differential equation (in this case Poisson's equation) given a driving function $f$:

$$
\nabla^2 v = f
$$

Unless we're involved in developing numerical libraries, it's not too often that we think about how these problems can be solved numerically. The fact there aren't very many blog posts about this kind of stuff shows how infrequently we think about these sorts of algorithms, so I wanted to give it a shot.

Before I get into it, I should mention that there are a number of different commercial software options for solving these types of equations, e.g. COMSOL multiphysics, but I don't like using proprietary software if I can avoid it - you can never look under the hood and see how it works. I looked at free software libraries like FENICS, but at the time I couldn't tell whether that project was abandoned or not, so I decided to write my own solver. At the time, I knew one crude way of doing this (Jacobi iteration, which I'll get to later) that I learned back in undergrad. But after spending a few minutes waiting for my Jacobi solver to finish with a simple 1D problem, my patience wore out and I decided to look at other options. One of these approaches - multigrid - is so cool that I had to share it here.

# Defining the Problem

I have a grid of equally spaced points with a charge distribution $\rho$ defined at each point:

![problem_layout]

and so on. The goal is to find the voltage $v$ everywhere, where the voltage is governed by Poisson's equation, mentioned above. The context of electromagnetism (where it's most commonly encountered) it is written

$$
\begin{align}
\nabla^2 v = -\frac{\rho}{\epsilon}
\label{eq:ref1}
\end{align}
$$

but for our purposes, I'm just going to use $f\equiv-\frac{\rho}{\epsilon}$. Since we're interested in solving for the value of $v$ on a set of discrete points, the first step in tackling the problem is to change the laplacian, which acts on a _continuous_ scalar field, into a _discrete_ operator. Using the central finite differences scheme, a derivative can be approximated by

$$
\frac{\mathrm{d}v}{\mathrm{d}x} \approx \frac{v(x+\frac{1}{2}\Delta x) - v(x-\frac{1}{2}\Delta x)}{\Delta x}
$$

The 1D laplacian can then be found pretty easily by applying this definition twice:

$$
\nabla^2 v = \frac{\mathrm{d}}{\mathrm{d}x} \frac{\mathrm{d}v}{\mathrm{d}x} \approx \frac{\frac{\mathrm{d}v}{\mathrm{d}x}(x+\frac{1}{2}\Delta x) - \frac{\mathrm{d}v}{\mathrm{d}x}(x-\frac{1}{2}\Delta x)}{\Delta x} = \frac{v(x+\Delta x) - 2v(x) + v(x-\Delta x)}{\Delta x^2}
$$

Since it's annoying to write $v(x+\Delta x)$ everywhere, I'm just going to use the notation that $v_i$ for the value of the voltage at $x_i$, which means that $v(x+\Delta x) \rightarrow v_{i+1}$, etc. Now the discrete laplacian is just

$$
\begin{align}
\nabla^2 v \,\,\rightarrow\,\, & \frac{v_{i+1} - 2v_i + v_{i-1}}{\Delta x^2}, \,\,\,\, 1 \le i \le n-1 \\
& v_0 = v_n = 0
\end{align}
$$

Rewriting this in terms of matrices allows us to reduce the amount of notation even further, making $\ref{eq:ref1}$:

$$
\begin{align}
\frac{1}{\Delta x^2}\begin{pmatrix} 1 & -2 & 1 & \, & \, & \, & \, & \, \\ & \, 1 & -2 & 1 & \, & \, & \, & \, \\ \, & \, & \, & \, & \ddots \, & \, & \, & \\ \, & \, & \, & \, & \, & 1 & -2 & 1 \end{pmatrix}
\begin{pmatrix} v_1 \\ v_2 \\ \vdots \\ v_{n-1} \end{pmatrix}
 = \begin{pmatrix} f_1 \\ f_2 \\ \vdots \\ f_{n-1} \end{pmatrix} \\
Av = f
\label{eq:ref2}
\end{align}
$$

where $A$ is the discretized laplacian we found above. Of course, \ref{eq:ref2} can be solved by inverting $A$, i.e. $v = A^{-1}f$, but in general we are talking about problems large enough that directly computing $A^{-1}$ is impractical (or at least really inefficient).

# Jacobi Iteration

The simplest iterative method for solving $\ref{eq:ref2}$ is _Jacobi iteration_. Let's take a look at $\ref{eq:ref2}$: it would be great if we could just invert $A$ to find the solution directly, but that's too hard, because this matrix has off-diagonal elements. If it only had nonzero elements along the diagonal, then we could find $A^{-1}$ easily - the inverse of a square diagonal matrix $D$ is just $D^{-1} = 1/D_{ij}$, with $ij$ representing the matrix indices. So we begin by trying to break $A$ into two matrices - $D$, with all the diagonal elements, and $Q$, with all the off-diagonal elements:

$$
A = D-Q
$$

Then, $Av=f$ becomes

$$
\begin{align}
(D-Q)v &= f \\
Dv &= Qv + f\\
v &= D^{-1}Qv + D^{-1}f
\label{eq:ref3}
\end{align}
$$

In this scheme, we start by making an initial guess $V^0_i$ at the solution, and plugging this into the right hand side to get the first iterative improvement $v^1_i$. We then plug that back in to the right hand side, and calculate again to improve that solution iteratively. This works because the improved solution $v^1_i$ is found from the values of the neighboring points at the last iteration:

$$
v^1_i = D^{-1}Qv^0_i + D^{-1}f = \frac{1}{2}(v^0_{i-1} + v^0_{i+1} + f_i)
$$

This is pretty good, because now the problem is just turning the crank as fast as the computer will go. But there's actually a neat trick which will speed up how fast this solution converges: instead of using the RHS of $\ref{eq:ref3}$ as the improved solution, we can treat it as an intermediate value: $v^* = D^{-1}Qv^0_i + D^{-1}f$. We then mix $v^*$ with part of the original solution to get the next iteration,

$$
v^1_i = wv^*_i + (1-w)v^0_i
$$

where $w$ is a constant less than $1$. This is the *weighted Jacobi method*, and it's faster than standard Jacobi iteration. When $w=1$, you can see that the original Jacobi iteration is recovered.

# Why Is This So Slow???

[problem_layout]: /assets/images/multigrid/problem_layout.png
{: .align-center}