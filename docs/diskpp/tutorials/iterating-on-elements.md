---
title: Iterating on elements
parent: Getting started with DiSk++
nav_order: 2
compress_html: false
---

# Tutorial 1: creating a mesh and iterating on its elements

This tutorial will guide you in creating a mesh and iterating on its elements.

{: .highlight}
Code for this example: `apps/diskpp_tutorials/diskpp_tutorial_1.cpp`

DiSk++ can read and use various kinds of mesh. It can also generate 

## Creating a mesh on the unit square


### Simplicial 2D meshes
```cpp
using mesh_type = disk::simplicial_mesh<T,2>;
mesh_type msh;
auto mesher = disk::make_simple_mesher(msh);
for (auto nr = 0; nr < num_refs; nr++)
    mesher.refine();
```

### Simplicial 3D meshes
```cpp
using mesh_type = disk::simplicial_mesh<T,3>;
mesh_type msh;
auto mesher = disk::make_simple_mesher(msh);
for (auto nr = 0; nr < num_refs; nr++)
    mesher.refine();
```

### FVCA5 hexagonal pattern
```cpp
using mesh_type = disk::generic_mesh<T,2>;
mesh_type msh;
auto mesher = disk::make_fvca5_hex_mesher(msh);
mesher.make_level(num_refs);
```