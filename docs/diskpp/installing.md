---
title: Building DiSk++
parent: DiSk++
nav_order: 2
---


# Building and installing DiSk++
{: .no_toc }

DiSk++ is developed on and for Linux. Windows is explicitly not supported.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites
There are some dependencies that need to be met in order to build and use DiSk++
successfully. Below, the instructions for a Debian-based distribution: it should
be easy to adapt them to your favorite distro.

 * A recent C++ compiler supporting C++20 (gcc >= 11, clang >= 15). On a Debian-based distribution this amounts to `apt install build-essential`
 * A recent CMake (https://cmake.org/). `apt install cmake`
 * Eigen (https://eigen.tuxfamily.org/). `apt install libeigen3-dev`
 * Git (https://git-scm.com/). `apt install git`
 * Intel MKL (https://www.intel.com/). In Debian it is available in the non-free repositories, that have to be enabled in your `/etc/apt/sources.list`. Then, an `apt install libmkl-dev` should be sufficient.
 * MUMPS (https://mumps-solver.org/index.php). `apt install libmumps-dev libmumps-seq-dev`
 * Lua (https://www.lua.org/). `apt install liblua5.4-0 liblua5.4-dev`
 * Silo (https://github.com/LLNL/Silo). `apt install libsiloh5-0 libsilo-dev`
 * VisIt (https://visit-dav.github.io/visit-website/). This is a scientific visualization software that is not available in the Debian repositories; precompiled versions can be downloaded from the website.
 * GMSH (https://gmsh.info/). `apt install gmsh libgmsh-dev`
 

## Building DiSk++
