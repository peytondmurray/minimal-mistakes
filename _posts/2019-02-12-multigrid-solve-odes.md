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
\begin{align}
-\nabla^2 v = f
\label{eq:poisson}
\end{align}
$$

Unless we're involved in developing numerical libraries, it's not too often that we think about how these problems can be solved numerically. The fact there aren't very many blog posts about this kind of stuff shows how infrequently we think about these sorts of algorithms, so I wanted to give it a shot.

Before I get into it, I should mention that there are a number of different commercial software options for solving these types of equations, e.g. COMSOL multiphysics, but I don't like using proprietary software if I can avoid it - you can never look under the hood and see how it works. I looked at free software libraries like FENICS, but at the time I couldn't tell whether that project was abandoned or not, so I decided to write my own solver. At the time, I knew one crude way of doing this (Jacobi iteration, which I'll get to later) that I learned back in undergrad. But after spending a few minutes waiting for my Jacobi solver to finish with a simple 1D problem, my patience wore out and I decided to look at other options. One of these approaches - multigrid - is so cool that I had to share it here.

# Defining the Problem

I have a grid of equally spaced points with a charge distribution $\rho$ defined at each point:

![problem_layout]{:class="img-responsive"}

and so on. The goal is to find the voltage $v$ everywhere, where the voltage is determined by Poisson's equation ($\ref{eq:poisson}$). In the context of electromagnetism (where it's often encountered) it is written

$$
\begin{align}
-\nabla^2 v = \frac{\rho}{\epsilon}.
\label{eq:ref1}
\end{align}
$$

Since we're interested in solving for the value of $v$ on a set of discrete points, the first step in tackling the problem is to change the laplacian, which acts on a _continuous_ scalar field, into a _discrete_ operator. Using the central finite differences scheme, a derivative can be approximated by

$$
\frac{\mathrm{d}v}{\mathrm{d}x} = v' \approx \frac{v(x+\frac{1}{2}\Delta x) - v(x-\frac{1}{2}\Delta x)}{\Delta x}
$$

The 1D laplacian can then be found pretty easily by applying this definition twice:

$$
\nabla^2 v = \frac{\mathrm{d}v'}{\mathrm{d}x} \approx \frac{v'(x+\frac{1}{2}\Delta x) - v'(x-\frac{1}{2}\Delta x)}{\Delta x} = \frac{v(x+\Delta x) - 2v(x) + v(x-\Delta x)}{\Delta x^2}
$$

Since it's annoying to write $v(x+\Delta x)$ everywhere, I'm just going to use $v_i$ for the value of the voltage at $x_i$, which means that $v(x+\Delta x) \rightarrow v_{i+1}$, etc. Now the LHS of $(\ref{eq:ref1})$ is just

$$
\begin{align}
-\nabla^2 v \,\,\rightarrow\,\, & \frac{-v_{i+1} + 2v_i - v_{i-1}}{\Delta x^2}, \,\,\,\, 1 \le i \le n-1 \\
& v_0 = v_n = 0
\end{align}
$$

Rewriting this in terms of matrices allows us to reduce the amount of notation even further, making $(\ref{eq:ref1})$:

$$
\begin{align}
\begin{pmatrix}
2 & -1 & \, & \, & \, & \, \\
-1 & 2 & -1 & \, & \, & \, \\
\, & \, & \, & \ddots & \, & \, \\
\, & \, & \, & \, & -1 & 2
\end{pmatrix}
\begin{pmatrix} v_1 \\ v_2 \\ \vdots \\ v_{n-1} \end{pmatrix}
 &= \begin{pmatrix} f_1 \\ f_2 \\ \vdots \\ f_{n-1} \end{pmatrix}
\end{align}
$$

or more concisely,

$$
\begin{align}
Av = f
\label{eq:ref2}
\end{align}
$$

where $A$ is the (negative) discretized laplacian we found above and $f\equiv \rho \Delta x^2 / \epsilon$. Of course, ($\ref{eq:ref2}$) can be solved by inverting $A$, i.e. $v = A^{-1}f$, but in general we are talking about problems large enough that directly computing $A^{-1}$ is impractical (or at least really inefficient). This turns out to be the case even for very small systems.

# Jacobi Iteration

The simplest iterative method for solving ($\ref{eq:ref2}$) is _Jacobi iteration_. Let's take a look at ($\ref{eq:ref2}$): it would be great if we could just invert $A$ to find the solution directly, but that's too hard because this matrix has off-diagonal elements. If the only nonzero elements were along the diagonal, finding $A^{-1}$ would be easy; the reciprocal of a diagonal matrix $D_{ii}^{-1} = 1/D_{ii}$. So we begin by trying to break $A$ into two matrices: $D$, with all the diagonal elements, and $Q$, with all the off-diagonal elements:

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

We start by making an initial guess $V^0_i$ at the solution, then plugging this into the right hand side to get the first iterative improvement $v^1_i$. The result is then plugged back in to the right hand side, and so on, to improve the solution iteratively. Notice that the improved solution $v^1_i$ is found from the values of the neighboring points at the last iteration:

$$
\begin{align}
v^1_i = D^{-1}Qv^0_i + D^{-1}f = \frac{1}{2}(v^0_{i-1} + v^0_{i+1} + f_i)
\label{eq:ref4}
\end{align}
$$

This is pretty good, because now the problem is just turning the crank as fast as the computer will go. But there's actually a neat trick which will speed up how fast this solution converges: instead of using the RHS of ($\ref{eq:ref3}$) as the improved solution, we can treat it as an intermediate value: $v^* = D^{-1}Qv^0_i + D^{-1}f$. We then mix $v^*$ with part of the original solution to get the next iteration,

$$
v^1_i = wv^*_i + (1-w)v^0_i
$$

where $w$ is a constant less than $1$. This is the *weighted Jacobi method*, and it's faster than standard Jacobi iteration. When $w=1$, the original Jacobi iteration is recovered.

<video autoplay="autoplay" loop="loop" width="1024" height="480">
  <source src="/assets/images/multigrid/jacobi.mp4" type="video/mp4">
</video>

On the left side is the actual value of $f$ plotted as a function of $x$ in blue, with $Av$ shown in black. On the right is the $v$ plotted as a function of $x$. The initial guess $v^0$ is just zero, but with each iteration it gets more rounded, and $Av$ converges toward $f$ (the difference $f-Av$ is called the _error_, but we'll get to that later).

Things get [a little more complicated](#appendix-dirichlet-boundary-conditions) if you want to find out what happens if we apply some voltage at the boundary, but I've left the details in the extra section at the bottom, an the math that follows still applies.

# Why Is This So Slow???

Going back to ($\ref{eq:ref4}$) you can see that at each iteration, the value of $v_i$ is only affected by neighboring points. It therefore takes a lot of iterations - $n$ iterations for a grid of $n$ points - for values of $v$ on one end of the lattice to have any effect on the other end. This makes the Jacobi method great for relaxing initial guesses like this:

<video autoplay="autoplay" loop="loop" width="1024" height="480">
  <source src="/assets/images/multigrid/jacobi_highfreq.mp4" type="video/mp4">
</video>

See how quickly it suppresses the high frequency components of the initial guess? After only a few iterations $v$ is relaxed into a more sensible form, but it takes a _lot_ more iterations to actually get it close to the real solution. On the other hand, it's terrible for intial guesses like this:

<video autoplay="autoplay" loop="loop" width="1024" height="480">
  <source src="/assets/images/multigrid/jacobi_lowfreq.mp4" type="video/mp4">
</video>

which only have low frequencies; it just takes too many iterations for it to get smoothed out and converge. This is the most important point about the Jacobi method: it's only good at smoothing out trial solutions which have high frequencies - it's useless at anything else.

# Multigrid

At this point you might complain that the convergence in the examples above is slow because I used obviously bad initial guesses for $v$, and you'd be right. If our initial guesses were better, we wouldn't have to spend so much time iterating (of course, if your initial guess _is the exact solution_, you don't have to iterate at all...). We already know the convergence rate of the Jacobi method depends strongly on how many points are in the grid; so instead of trying to find the solution on the original grid with $n$ points (with spacing $h$), let's first use the Jacobi method on a coarse grid with $n/2$ points (with spacing $2h$) to try to improve our initial guess.

![coarse_grid_jacobi]{:class="img-responsive"}

After iterating the Jacobi solver 100 times, the error $e=f-Av$ is much smaller on the coarse $2h$ grid than it is on the original $h$ grid (though a lot more iterations are still needed to get close to the actual answer). The efficiency gains can be even greater if you move to even more coarse grids:

![error_vs_gridsize]{:class="img-responsive"}

This behavior is really simple to understand when you look at what happens when you move from a fine grid to a coarse grid:

![fine_to_coarse]{:class="img-responsive"}

Slowly varying functions on the fine grid _appear_ to vary more rapidly on the coarse grid! So this is why the Jacobi method is better on coarser grids, and it motivates the main steps of the multigrid method, which I think are best represented as a schematic:

![one_level_v]{:class="img-responsive"}

Of course you don't have to stop with one grid-coarsening step - in fact, it's best to move to a coarse grid, iterate the Jacobi solver a few times, then move to a coarser grid, Jacobi-iterate a few times, and so on. At some point, the coarse grid will only consist of a few points; then you move back to a finer grid, Jacobi-iterate a few times, move to a finer grid, Jacobi iterate a few more times, and so on until you get back to the original grid spacing:

![multi_level_v]{:class="img-responsive"}

This is called the V-cycle multigrid method (VMG), and it allows these sorts of problems to be solved way faster than using the Jacobi method on the original problem alone. In practice, it turns out to be even better to start on the coarsest grid possible, and follow this sort of pattern, called Full Multigrid (FMG):

![full_multigrid]{:class="img-responsive"}

# Moving Between Grids

Up until now, I've swept the details of switching between different grid spacings under the rug. Of course, it's an important part of the machinery behind multigrid, but it actually turns out to be pretty simple - you just use interpolation. When moving $v$ from a grid with spacing $2h$ to one with spacing $h$, the process is called _restriction_:

$$
v_i^{2h} =
\begin{cases}
v_{2i}^{h} & \text{for even } i \\
\frac{1}{2}(v_{2i-1}^{h} + v_{2i+1}^{h}) & \text{for odd } i
\end{cases}
$$

Under this scheme, if a point in the coarse grid lies at the same $x$ position as a point in the fine grid, we just take the value of $v$ there. Otherwise, you just average the adjacent points in the fine grid to find the value in the coarse grid. Best of all, if you want to go from a coarse grid to a finer grid spacing, you just do the reverse process, called _prolongation_:

$$
v_i^{h} =
\begin{cases}
v_{i/2}^{2h} & \text{for even } i \\
\frac{1}{2}(v_{(i-1)/2}^{2h} + v_{(i+1)/2}^{2h}) & \text{for odd } i
\end{cases}
$$

The prolongation and restriction operations can be represented as matrices:

$$
\begin{align*}
I_{h\rightarrow 2h} &=
\frac{1}{2}
\begin{pmatrix}
0.5 & 1 & 0.5 & \, & \, & \, & \, & \, \\
\, & 0.5 & 1 & 0.5 & \, & \, & \, & \, \\
\, & \, & \, & \, & \ddots & \, & \, & \, \\
\, & \, & \, & \, & \, & 0.5 & 1 & 0.5 \\
\end{pmatrix} \;\;\; & \text{(Restriction)} \\
I_{2h\rightarrow h} &=
\begin{pmatrix}
0.5 & \, & \, & \, \\
1 & \, & \, & \, \\
0.5 & 0.5 & \, & \, \\
\, & 1 & \, & \, \\
\, & 0.5 & \, & \, \\
\, & \, & \ddots & \, \\
\, & \, & \, & 0.5 \\
\, & \, & \, & 1 & \\
\, & \, & \, & 0.5 \\
\end{pmatrix} & \text{(Prolongation)}
\end{align*}
$$

You can see that the prolongation and restriction operators are related by $I_{h\rightarrow 2h} = 0.5I_{2h\rightarrow h}^T$, which makes computing them simple. Now moving between coarse and fine grids is easy:

$$
\begin{align*}
v_{2h} &= I_{h\rightarrow 2h}v_h \\
f_{2h} &= I_{h\rightarrow 2h}f_h \\
A_{2h} &= I_{h\rightarrow 2h} A_h I_{2h\rightarrow h}
\end{align*}
$$

# How much faster is multigrid?

I wanted to benchmark the Jacobi, VMG, and FMG solvers, so I ran them on a few different grid sizes, each starting from the same initial guess $v_0$. For the Jacobi solver alone I ran 1000 iterations for each data point, while for the VMG and FMG solvers, I ran 10 Jacobi iterations at each grid coarsening/refinement step (!!). Then I took the sum square error, $\sum (f - Av)^2$, and plotted it against the grid size. As you can see, there's way less error in the approximate solutions generated by the VMG and (especially) the FMG solvers. This shows how astonishingly effective even just a few Jacobi iterations on very coarse grids can be for helping convergence, and highlights how slowly the trial solution converges when trying to Jacobi iterate on a gigantic grid.

![efficiency]

I've purposely left out details of the implementation because I thought the concepts behind the algorithm are the most important part, but if you're interested in seeing how I generated these plots you can [view my implementation on github][github]. I've made an effort to optimize the code within reason.

I'd like to write more about some of the algorithms hidden in the software we rely on in the future, and I hope you found this brief description of multigrid useful. If you want to learn more, Bill Briggs wrote [an excellent book][briggs] on the subject which goes into way more detail than I have here.

# Appendix: Dirichlet Boundary Conditions

To include the effects of fixed voltage at the boundary points $v_0$ and $v_n$ into $(\ref{eq:ref2})$, we only need to write down the discrete Poisson's equation at the points _adjacent_ to the edge:

$$
\begin{align}
v_0 - 2v_1 + v_2 = f_1 \,\,\,\, &\rightarrow \,\,\,\, f_1 - v_0 = 2v_1 + v_2 \\
v_{n-2} - 2v_{n-1} + v_n = f_{n-1} \,\,\,\, &\rightarrow \,\,\,\, f_{n-1} - v_{n} = v_{n-2} - 2v_{n-1}
\end{align}
$$

This means that you can use the $A$, $I_{h2h}$, and $I_{2hh}$ operators and all of the associated machinery above with only a slight modification to the $f$-vector:

$$
f =
\begin{pmatrix}
f_1 - v_0 \\
\vdots \\
f_{n-1} - v_{n-1}
\end{pmatrix}
$$

[github]: https://github.com/peytondmurray/mugrid
[briggs]: https://dx.doi.org/10.1137/1.9780898719505

[problem_layout]: /assets/images/multigrid/problem_layout.svg
{: .align-center}
[coarse_grid_jacobi]: /assets/images/multigrid/coarse_grid_jacobi.svg
{: .align-center}
[error_vs_gridsize]: /assets/images/multigrid/error_vs_gridsize.svg
{: .align-center}
[fine_to_coarse]: /assets/images/multigrid/fine_to_coarse.svg
{: .align-center}
[one_level_v]: /assets/images/multigrid/one_level_v.svg
{: .align-center}
[multi_level_v]: /assets/images/multigrid/multi_level_v.svg
{: .align-center}
[full_multigrid]: /assets/images/multigrid/full_multigrid.svg
{: .align-center}
[efficiency]: /assets/images/multigrid/efficiency.svg
{: .align-left}
