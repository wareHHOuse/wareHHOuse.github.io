---
title: Iterating on elements
parent: Getting started with DiSk++
nav_order: 2
---


# Tutorial 1: creating a mesh and iterating on its elements

This tutorial will guide you in creating a mesh and iterating on its elements.

{: .highlight}
Code for this example: `apps/diskpp_tutorials/diskpp_tutorial_1.cpp`

DiSk++ can read and use various kinds of mesh. It can also generate 

## Creating a mesh on the unit square

{% highlight cpp linenos %}
using mesh_type = disk::simplicial_mesh<T,2>;
mesh_type msh;
auto mesher = disk::make_simple_mesher(msh);
for (auto nr = 0; nr < num_refs; nr++)
mesher.refine();
{% endhighlight %}

test test