---
title: LAMMPS Installation in Manjaro
layout: single
author_profile: true
read_time: true
share: true
date: '2018-11-03 14:30:00 -0800'
categories: coding
---

I've been interested in doing some [molecular dynamics][md] (MD) simulations for a project I've been thinking about for a long time, but it's only recently that I've had the time to start tinkering with MD simulation packages. There are a few large projects to choose from if you're looking to get into MD simulations, with [GROMACS][GROMACS] and [LAMMPS][LAMMPS] being two of the more widely used packages. Although the literature suggests that GROMACS can be used for MD simulations of crystalline systems, it just seemed to me like it has been primarily used for simulating biological systems - proteins and that kind of stuff. So I decided to check out LAMMPS, an MD simulator maintained by Sandia, with contributions from people all over the world.

LAMMPS comes with a lot of extensions which add various functionality to the simulator, so building the source code is recommended because you can choose what packages you need for your specific application. Depending on your system, you can also find prebuilt binaries, but to take full advantage of the optimizations available to your system, building from source is the best. Fortunately, LAMMPS uses the [CMAKE][CMAKE] meta-build system. If you're not familiar with CMAKE, it's a tool which creates the files necessary to compile source code. On Linux, it builds makefiles; on Windows, you can use it to create Visual C++ project files. It can do lots of stuff beyond just creating the instructions for compiling and linking your source code for whatever platform you are using, but it's not the focus of this post, so I'm going to move on.

Since LAMMPS can be built with tons of different packages, based on the description of the package in the LAMMPS documentation, I took an educated guess as to whether or not I was going to want to have it, and made a shortlist of what to build. Each package has a short summary of what it does as a note to myself. Packages with a * denote ones that require additional CMAKE variables to be defined, with the values needed for my system noted below.


# Packages

* ASPHERE - Computes, time-integration fixes, and pair styles for aspherical particle models including ellipsoids, 2d lines, and 3d triangles.
* BODY - Body-style particles with internal structure. Computes, time-integration fixes, pair styles, as well as the body styles themselves. See the Howto body doc page for an overview.
* CLASS2 - Bond, angle, dihedral, improper, and pair styles for the COMPASS CLASS2 molecular force field.
* CORESHELL - Compute and pair styles that implement the adiabatic core/shell model for polarizability.
* DIPOLE - An atom style and several pair styles for point dipole models with short-range or long-range interactions.
* GPU* - GPU pair styles and other stuff. KOKKOS uses GPU-enabled styles

    * GPU_API=cuda
    * GPU_ARCH=sm_60
    * CUDPP_OPT=yes

* GRANULAR - Pair styles and fixes for finite-size granular particles, which interact with each other and boundaries via frictional and dissipative potentials.
* KIM* - Knowledge Base for Interatomic Models repository of interatomic potentials.

    * DOWNLOAD_KIM=yes

* KOKKOS* - Parallelization (including GPU) for lots of models.

    * KOKKOS_ARCH=HSW;Pascal61
    * KOKKOS_ENABLE_CUDA=yes
    * KOKKOS_ENABLE_OPENMP=yes
    * CMAKE_CXX_COMPILER=\<path to nvcc_wrapper included with LAMMPS source\>

* KSPACE - Long range coulombic solvers
* MANYBOCY - Manybody and bond-order potentials
* MC - Several fixes and a pair style that have Monte Carlo (MC) or MC-like attributes.
* MISC - A variety of compute, fix, pair, dump styles with specialized capabilities that don’t align with other packages.
* OPT - Optimized pair styles for multiple cores
* PERI - Particle-based meshless continuum model
* PYTHON - python command which allows python code to be executed from a LAMMPS input script

    * PYTHON_EXECUTABLE=\<path to python bin\>

* QEQ - Fixes for charge equilibration via different algorthims
* REPLICA - Nudged elastic band methods, other algorithms used in running multiple LAMMPS simulations
* RIGID - Fixes which enforce rigid constraints on collections of atoms or particles.
* SHOCK - Fixes for running impact simulations where a shock-wave passes through a material.
* SPIN - Model atomic magnetic spins classically, coupled to atoms moving in the usual manner via MD. Various pair, fix, and compute styles.
* USER-COLVARS - COLVARS stands for collective variables, which can be used to implement various enhanced sampling methods, including Adaptive Biasing Force, Metadynamics, Steered MD, Umbrella Sampling and Restraints.
* USER-DIFFRACTION - Two computes and a fix for calculating x-ray and electron diffraction intensities based on kinematic diffraction theory.
* USER-DRUDE - Fixes, pair styles, and a compute to simulate thermalized Drude oscillators as a model of polarization.
* USER-FEP - This package provides methods for performing free energy perturbation simulations by using a fix adapt/fep command with soft-core pair potentials, which have a “soft” in their style name.
* USER-MISC - A potpourri of (mostly) unrelated features contributed to LAMMPS by users. Each feature is a single fix, compute, pair, bond, angle, dihedral, improper, or command style.
* USER-MEAMC - A pair style for the modified embedded atom (MEAM) potential translated from the Fortran version in the MEAM package to plain C++. 
* USER-OMP - Hundreds of pair, fix, compute, bond, angle, dihedral, improper, and kspace styles which are altered to enable threading on many-core CPUs via OpenMP directives.
* USER-PHONON - A fix phonon command that calculates dynamical matrices, which can then be used to compute phonon dispersion relations, directly from molecular dynamics simulations.
* USER-QTB - Two fixes which provide a self-consistent quantum treatment of vibrational modes in a classical molecular dynamics simulation.
* USER-REAXC - A pair style which implements the ReaxFF potential in C/C++ (in contrast to the REAX package and its Fortran library). ReaxFF is universal reactive force field.
* USER-TALLY - Several compute styles that can be called when pairwise interactions are calculated to tally information (forces, heat flux, energy, stress, etc) about individual interactions.
* USER-VTK - VTK output

# Get it in one CMAKE command

If the LAMMPS git repo is cloned, and a `build` directory is created in the root directory, the CMAKE part of the building process can be completed in one go. You'll obviously have to change the part about python executable, but having it in one command is nice.

`
cmake -D PKG_ASPHERE=yes -D PKG_BODY=yes -D PKG_CLASS2=yes -D PKG_CORESHELL=yes -D PKG_DIPOLE=yes -D PKG_GPU=yes -D GPU_API=cuda -D GPU_ARCH=sm_60 -D CUDPP_OPT=yes -D PKG_GRANULAR=yes -D PKG_KIM=yes -D DOWNLOAD_KIM=yes -D PKG_KSPACE=yes -D PKG_MANYBODY=yes -D PKG_MC=yes -D PKG_MISC=yes -D PKG_OPT=yes -D PKG_PERI=yes -D PKG_PYTHON=yes -D PKG_QEQ=yes -D PKG_REPLICA=yes -D PKG_RIGID=yes -D PKG_SHOCK=yes -D PKG_SPIN=yes -D PKG_USER-COLVARS=yes -D PKG_USER-DIFFRACTION=yes -D PKG_USER-DRUDE=yes -D PKG_USER-FEP=yes -D PKG_USER-MISC=yes -D PKG_USER-MEAMC=yes -D PKG_USER-OMP=yes -D PKG_USER-PHONON=yes -D PKG_USER-QTB=yes -D PKG_USER-REAXC=yes -D PKG_USER-TALLY=yes -D PKG_USER-VTK=yes -D PYTHON_EXECUTABLE=\<path to python bin\> -D BUILD_OMP=yes -D BUILD_EXE=yes -D BUILD_LIB=yes -D BUILD_SHARED_LIBS=yes -D DOWNLOAD_KIM=yes ../cmake
`

# KOKKOS Broken?

That being said, I could not get KOKKOS to compile almost no matter what I did. I followed the instructions on the LAMMPS website only to discover that gcc 8 is not supported. I tried using gcc 4.9, which is on the Arch User Repository, but that didn't work either. In any case, choosing not to build KOKKOS seems bad, since it offers what seems like a lot of GPU-enabled features. I'll probably revisit this issue in the future, and if I find anything else I'll update this post.


[md]: https://en.wikipedia.org/wiki/Molecular_dynamics
[GROMACS]: http://www.gromacs.org
[LAMMPS]: https://lammps.sandia.gov
[CMAKE]: https://cmake.org