---
title: Evaluating functions and plotting data
parent: Getting started with DiSk++
nav_order: 3
---

# Tutorial 2: evaluating functions and plotting data

{: .highlight}
Code for this example: `apps/diskpp_tutorials/diskpp_tutorial_2.cpp`

DiSk++ uses the [Silo library](http://software.llnl.gov/Silo/) to output data. Silo files can then be opened with [VisIt](https://visit-dav.github.io/visit-website/). One day we will support also VTK/Paraview, but that day has not come yet.

This second tutorial is meant to make you comfortable with the DiSk++ interface to Silo.

## Defining the functions to plot

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