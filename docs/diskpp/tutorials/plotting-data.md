---
title: Evaluating functions and plotting data
parent: Getting started with DiSk++
nav_order: 3
---

# Tutorial 2: evaluating functions and plotting data

{: .highlight}
Code for this example: `apps/diskpp_tutorials/diskpp_tutorial_2.cpp`

DiSk++ uses the [Silo library](http://software.llnl.gov/Silo/) to output data. Silo files can then be opened with [VisIt](https://visit-dav.github.io/visit-website/). It is strongly recommended to review the VisIt documentation before starting this DiSk++ tutorial.

One day we will support also VTK/Paraview, but that day has not come yet. In any case, Paraview can open Silo files without problems.

This second tutorial is meant to make you comfortable with the DiSk++ interface to Silo.

## Defining the functions to plot

In DiSk++, points are represented using the `disk::point` template. Scalar-valued functions of a point are declared as follows:

```cpp
template<typename T>
auto fun(const disk::point<T,2>& pt)
{
    auto sx = std::sin(M_PI*pt.x());
    auto sy = std::sin(M_PI*pt.y());
    return sx*sy;
}

template<typename T>
auto fun(const disk::point<T,3>& pt)
{
    auto sx = std::sin(M_PI*pt.x());
    auto sy = std::sin(M_PI*pt.y());
    auto sz = std::sin(M_PI*pt.z());
    return sx*sy*sz;
}
```

On the other hand, vector-valued functions of a point are declared as follows:

```cpp
template<typename T>
auto fun_grad(const disk::point<T,2>& pt)
{
    using gv = Eigen::Matrix<T, 2, 1>;
    gv ret;
    auto sx = std::sin(M_PI*pt.x());
    auto cx = std::cos(M_PI*pt.x());
    auto sy = std::sin(M_PI*pt.y());
    auto cy = std::cos(M_PI*pt.y());
    ret(0) = cx*sy;
    ret(1) = sx*cy;
    return ret;
}

template<typename T>
auto fun_grad(const disk::point<T,3>& pt)
{
    using gv = Eigen::Matrix<T, 3, 1>;
    gv ret;
    auto sx = std::sin(M_PI*pt.x());
    auto cx = std::cos(M_PI*pt.x());
    auto sy = std::sin(M_PI*pt.y());
    auto cy = std::cos(M_PI*pt.y());
    auto sz = std::sin(M_PI*pt.z());
    auto cz = std::cos(M_PI*pt.z());
    ret(0) = cx*sy*sz;
    ret(1) = sx*cy*sz;
    ret(2) = sx*sy*cz;
    return ret;
}
```

## Evaluating the functions

In both cases you can notice that there is a 2D variant and a 3D variant. Once you have a mesh loaded and the functions defined, you can start evaluating them on the mesh elements. For example, the above functions can be evaluated on the barycenter of each cell as follows:

```c++
for (auto& cl : msh) {
    auto pt = barycenter(msh, cl);
    auto val = fun(pt);
    auto grad_val = fun_grad(pt);
}
```
At each iteration the variables `val` and `grad_val` contain the values of `fun()` and its gradient at the barycenter of `cl`. Of course, to plot that data you need to collect it in some vector/matrix, so the code can be modified as follows:

```c++
static const size_t DIM = Mesh::dimension;
using gvs = Eigen::Matrix<T, Eigen::Dynamic, DIM>;

std::vector<T> fun_vals_zonal;
gvs grad_vals_zonal = gvs::Zero(msh.cells_size(), DIM);
size_t cell_i = 0;
for (auto& cl : msh) {
    auto pt = barycenter(msh, cl);
    fun_vals_zonal[cell_i] = fun(pt);
    grad_vals_zonal.row(cell_i++) = fun_grad(pt);
}
```

## Plotting the functions
You now have the vector `fun_vals_zonal` and the matrix `grad_vals_zonal`. Why the `zonal` suffix? Because you evaluated the functions at the barycenters, therefore in the plot VisIt will use the value at the barycenter for the whole cell. In that way, you get a piecewise constant plot of your function. In order to export that data to VisIt, we proceed as follows:
```c++
disk::silo_database db;     /* Declare the Silo database object */
db.create(output_filename); /* Create a new database */
db.add_mesh(msh, "mesh");   /* and store the mesh into it, with the name "mesh" */
/* Now add the scalar data to the mesh "mesh", storing it as a variable
   named "fun_zonal". */
db.add_variable("mesh", "fun_zonal", fun_vals_zonal, disk::zonal_variable_t);
/* and do the same with the gradient data, that gets stored in "grad_zonal" */
db.add_variable("mesh", "grad_zonal", grad_vals_zonal, disk::zonal_variable_t);
```

And here is what you should get in VisIt:

![Zonal function plot](/assets/tuto2plot.png){: width="70%" centered }

Of course it is possible also to use nodal variables, which are much more adapted to plot continuous approximations of functions. This possibility is covered in the code for this tutorial.