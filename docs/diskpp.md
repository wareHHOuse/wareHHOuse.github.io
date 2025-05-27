---
title: DiSk++
nav_order: 2
---

# DiSk++
DiSk++ is a C++ template library for the fast prototyping of Discontinuous Skeletal (DiSk) polyhedral discretization methods for PDEs. The main members of the DiSk family are:

* The Discontinuous Galerkin (DG) method
* The Hybridizable Discontinuous Galerkin (HDG) method
* The Hybrid High-Order (HHO) method

DiSk++ was initially born and is currently focused to support the development of the HHO method.

The mathematical formulation of polyhedral methods is in general independent of the spatial dimension (so it does not change if you switch between 1D, 2D and 3D) and independent of the shape of the mesh cells. DiSk++ aims to replicate this flexibility and this level of abstraction in software, giving the programmer an abstraction as close as possible to the mathematical writing of the method of choice.

Despite the "Discontinuous Skeletal" nature of DiSk++, partial support for other families of methods is available, in particular:
 
 * Finite Element Method (FEM)
 * Discrete Geometric Approach (DGA)
 * Virtual Element Method (VEM)

## The documentation
DiSk++ is quite different from your average Finite Element code, as it tries to make a balance between the needs of the mathematician and the implementor. Therefore, before attempting to use DiSk++, please be sure to understand its design principles, which are described in these pages. You are also encouraged to take the tutorials, which will gradually introduce you to the basic operations available in the library.

