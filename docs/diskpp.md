---
title: DiSk++
nav_order: 2
---

DiSk++ is a C++ template library for the fast prototyping of Discontinuous Skeletal (DiSk) polyhedral discretization methods for PDEs. The main members of the DiSk family are:

* The Discontinuous Galerkin (DG) method
* The Hybridizable Discontinuous Galerkin (HDG) method
* The Hybrid High-Order (HHO) method

DiSk++ was initially born and is currently focused to support the development of the HHO method.

The mathematical formulation of these methods is independent of the spatial
dimension (so it does not change if you switch between 1D, 2D and 3D) and
independent of the shape of the mesh cells. DiSk++ tries to replicate this flexibility and this level of abstraction in software.

Despite the "Discontinuous Skeletal" nature of DiSk++, partial support for other families of methods is available, in particular:
 
 * Finite Element Method (FEM)
 * Discrete Geometric Approach (DGA)
 * Virtual Element Method (VEM)
