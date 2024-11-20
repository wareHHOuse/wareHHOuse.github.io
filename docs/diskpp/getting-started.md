---
title: Getting started with DiSk++
parent: DiSk++
nav_order: 3
---


# Getting started with DiSk++
{: .no_toc }

DiSk++ aims to be a fast prototyping environment for Discontinuous Skeletal methods for PDEs. In this page we provide a high-level overview of the design decisions behind the library. Once you are comfortable with the general principles behind the design of DiSk++ you can start with the tutorials. 

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## DiSk++ design principles

In order to correctly use DiSk++, it is necessary to understand some of the ideas around which the library is built.

 * DiSk++ originally was born to support the Hybrid High-Order (HHO) method, but it supports polyhedral Discontinuous Skeletal (DiSk) methods in general, like Discontinuous Galerkin (DG), Hybrid Discontinuous Galerkin (HDG) and so on. Finite Elements-like methods (FEM, VEM) are not the main goals of the library, even if technically they are implementable.
 * DiSk++ is a library for the _fast prototyping_ of your ideas. DiSk++ is _not_ an HPC library and maximum speed it is _not_ its main goal.
 * As DiSk++ is built for DiSk methods on polyhedral meshes, the main geometrical concepts are the _cell_ and the _face_, independently of the dimension and the shape of the elements of the mesh. Therefore, in a 3D mesh, an hexahedron is a cell and its faces are **planar** quadrilaterals immersed in the three-dimensional space. Similarly, in a 2D mesh, a pentagon is a cell and its faces are straight segments. The "planarity" requirement on the faces is due to the original definition of the HHO method.
 * Reasoning only in terms of cells and faces, DiSk++ allows you to write your numerical methods following a "write once, run on any mesh" principle. The code for a 3D polyhedral mesh will run as-is on a mesh of 2D simplices or 1D segments.
 * All the operations in DiSk++ are _element-local_, in addition we try to maintain as few state as possible. This not optimal in terms of efficiency, but it allows the user to have a clear visibility on what it is going on.

## General DiSk++ structure

In order to give to the user a dimension-independent and shape-independent interface, DiSk++ employs the layered structure depicted in the figure below.

![DiSk++ layered architecture](/assets/layers.png){: width="70%" centered }

From the bottom to the top, the various layers have the following functions:

 * **Loaders:** meshes of course come in different formats. When you receive a file containing a mesh, the role of a loader is to read that file and transform it in one of the internals representations of DiSk++
 * **Storage classes:** Your mesh file can contain a 2D simplicial mesh, a 3D cartesian mesh, a 3D polyhedral mesh or whatever kind of mesh you want. However, if you get a simplicial mesh, you don't want to pay the overhead needed to handle fully polyhedral meshes. For this reason, DiSk++ employs different internal representations, which we call _storage classes_. Each storage class provides efficent storage for the corresponding type of mesh.
 * **Object storage:** As you don't want to deal with specific element shapes, in the layer above the elements of a specific storage class are mapped to the more general concepts of d-polytopes (a 0-polytope is a point, a 1-polytope is a line and so on).
 * **Mesh:** At this point we've abstracted away the shapes of the elements, however the spatial dimension is still there. Therefore we need to map the concepts of "cell" and "face" to the correct polytopes: in dimension d a cell is a d-polytope, whereas a face is a (d-1)-polytope
 * At this point we have abstracted away all the geometric information and we can finally have abstract operations on cells and faces

An example of the abstraction provided by DiSk++ is the code below: we iterate on all the cells of the mesh and for each cell we compute the diameter, the measure (volume in 3D, area in 2D, lenght in 1D) and the barycenter. Subsequently, we ask for all the faces of the current cell and we compute the same quantities as above. Here's the code:

```cpp
for (auto& cl : msh) /* for each cell in the mesh */
{
    std::cout << cl << std::endl;
    std::cout << "  Diameter: " << diameter(msh, cl) << std::endl;
    std::cout << "  Measure: " << measure(msh, cl) << std::endl;
    std::cout << "  Barycenter: " << barycenter(msh, cl) << std::endl;
    
    auto fcs = faces(msh, cl); /* get the faces of this cell */
    for (auto& fc : fcs) /* for each face */
    {
        std::cout << "  " << fc << std::endl;
        std::cout << "    Diameter: " << diameter(msh, fc) << std::endl;
        std::cout << "    Measure: " << measure(msh, fc) << std::endl;
        std::cout << "    Barycenter: " << barycenter(msh, fc) << std::endl;
    }
}

```

Another example of the abstraction provided by DiSk++ is the code below, which computes the local grad-grad term of an HHO discretization. Notice in particular that the mesh is fully generic in terms of dimension and element shape; notice in addition the calls to `integrate()` and compare them with the mathematical definition of the HHO reconstruction.

```cpp
template<typename Mesh, typename Space = hho_space<Mesh>>
auto
local_operator(const Mesh& msh, const typename Mesh::cell_type& cl,
    const degree_info& di)
{
    using namespace disk::basis;
    using T = typename Space::scalar_type;

    auto phiR = typename Space::cell_basis_type(msh, cl, di.reco);
    auto phiT = typename Space::cell_basis_type(msh, cl, di.cell);

    auto [szT, szF, szR] = space_dimensions<Space>(di);

    auto fcs = faces(msh, cl);
    auto num_faces = fcs.size();
    assert(szR > 0);
    auto rows = szR - 1;
    auto cols = szT + num_faces*szF;

    dynamic_matrix<T> stiffness = integrate(msh, cl, grad(phiR), grad(phiR));
    dynamic_matrix<T> lhs = stiffness.bottomRightCorner(szR-1, szR-1);
    dynamic_matrix<T> rhs = dynamic_matrix<T>::Zero(rows, cols);
    rhs.block(0,0,szR-1,szT) = stiffness.bottomLeftCorner(szR-1, szT);

    size_t offset = szT;
    for (const auto& fc : fcs)
    {
        auto n = normal(msh, cl, fc);
        auto phiF = typename Space::face_basis_type(msh, fc, di.face);

        rhs.block(0,offset,szR-1,szF) += 
            integrate(msh, fc, phiF, grad(phiR).dot(n)).block(1,0,szR-1,szF);

        rhs.block(0,0,szR-1,szT) -=
            integrate(msh, fc, phiT, grad(phiR).dot(n)).block(1,0,szR-1,szT);

        offset += szF;
    }

    dynamic_matrix<T> R = lhs.ldlt().solve(rhs);
    dynamic_matrix<T> A = rhs.transpose()*R;
    return std::pair(R, A);
}
```
