+++
title = "GSOC 2021 Final Report"
published = "2021-08-16"
+++

# GSOC 2021 Final Report

This blog will summarize the work I did in summer 2021 as part of [Google Summer of Code](https://summerofcode.withgoogle.com/). See [the project proposal](https://summerofcode.withgoogle.com/projects/#5463862406545408) and [the first post](/posts/post1) for the proposed work. In short, I contributed spatial simulation algorithms to [`DiffEqJump`](https://github.com/SciML/DiffEqJump.jl) -- a Julia module for simulating jump processes. Below is a rather detailed description of all the work I have done this summer. Follow the embedded links for more details.

## Interface for spatial SSAs

Since `DiffEqJump` did not have spatial solvers before this summer, a new interface had to be developed. It had to be consistent with the existing interface because it feeds into the rich ecosystem of `SciML`, and be flexible enough for more spatial SSAs to be added. After a lot of trial and error such an interface was settled on. In short, only two extra keyword arguments are required of the user over what is needed for an ordinary non-spatial problem. See [the previous post](/posts/post4) or [this tutorial](https://tutorials.sciml.ai/html/jumps/spatial.html) for a detailed description of the interface.

Apart from the interface that is exposed to the user, an internal interface for spatial SSAs was developed. The most notable elements of it are three `struct`s that keep track of spatial information needed by any spatial solver: `CartesianGrid`, `RxRates` and `HopRates`.

## Spatial Solvers

Two specialized spatial solvers were added to `DiffEqJump` -- `NSM` and `DirectCRDirect`. Additionally, a flattening method was added as fallback. See [post 4](\reflink{Solvers}) for a description of the two solvers and flattening. In short, `NSM` uses a binary heap to choose the next site to fire, resulting in $O(\log n)$ asymptotic runtime -- the time to update the heap; `DirectCRDirect` uses a clever priority table resulting in $O(1)$ runtime but with higher overhead; flattening treats species at different sites as distinct and treats diffusive hops as monomolecular reactions resulting in extremely high memory usage. The asymptotic runtime of flattened algorithms depend on the exact non-spatial SSA used but are not representative of real performance due to inefficient memory use.

Both specialized solvers perform better than the two best existing non-spatial solvers -- `RSSACR` and `DirectCR` as can be seen in the figure below.

~~~
<figure>
  <img src="/assets/SSAs_comparison_plot.png" width="120%" />
  <figcaption> Comparison of spatial SSAs on a big model </figcaption>
</figure>
~~~

## Spatial structs and utilities

There are now [four files](https://github.com/SciML/DiffEqJump.jl/tree/master/src/spatial) with utility functions and `struct`s for spatial solvers -- `utils.jl`, `topology.jl`, `hop_rates.jl` and `reaction_rates.jl`. 

The first one contains functions for doing various routines common to all or many spatial SSAs. For example [`update_state!`](https://github.com/SciML/DiffEqJump.jl/blob/master/src/spatial/utils.jl#L59) updates the current state. 

The second file contains several `struct`s to keep track of the topology of the system. Most notably, `CartesianGrid` represents a Cartesian Grid -- an interval, a rectangle, or a 3D equivalent of a rectangle (sometimes called hyperrectangle). See [post 3](/posts/post3) for a description of `CartesianGrid`. Additionally, any graph from `LightGraphs` can be passed in, allowing for simulations on unstructured meshes. This can be useful in epidemics simulations (representing a geographical region as a graph) and simulations on meshes obtained from triangulating a surface.

The last two files implement two analogous `struct`s -- `RxRates` to keep track of reactions and `HopRates` to keep track of spatial hops. The latter of the two is specifically optimized for `CartesianGrid`.

## Tests, tutorials, benchmarks, documentation

In order to ensure accuracy of the newly added solvers and utility structures [three tests](https://github.com/SciML/DiffEqJump.jl/tree/master/test/spatial) have been created. One tests utilities and the other two test the accuracy of the solvers. All three tests are passing.

A comprehensive [tutorial](https://tutorials.sciml.ai/html/jumps/spatial.html) has been written and added to [`SciMLTutorials`](https://github.com/SciML/SciMLTutorials.jl). This will help users get started with the new functionality.

A benchmarking [script](https://github.com/SciML/SciMLBenchmarks.jl/pull/298) has been written, and will be included in [`SciMLBenchmarks`](https://github.com/SciML/SciMLBenchmarks.jl). It uses a model from [a research paper](https://scholar.google.com/citations?view_op=view_citation&hl=en&user=Tf5L2NIAAAAJ&citation_for_view=Tf5L2NIAAAAJ:roLk4NBRz8UC) and compares the performance of the newly added spatial SSAs to the best existing SSAs. The figure above is generated by this script.

All functions and structures have docstrings in order to help future developers understand and extend the code.

## Further work

This [issue](https://github.com/SciML/DiffEqJump.jl/issues/189) on Github contains the roadmap of what has been done and what is left to do in the future. Some of the most important items to be implemented in the future are:

* escaping boundary condition
* other types of jumps such as constant-rate jumps
* spatially varying mass-action jumps
* Tooling for generating hopping rates for diffusion, advection-diffusion, and drift-diffusion
* spatially distributed reactions (e.g. CRDME)

## List of all Pull Requests

Here is the complete list of my pull requests to `DiffEqJump`. All of them have been merged.

* [Next Subvolume Method on Graphs](https://github.com/SciML/DiffEqJump.jl/pull/183) -- implemented `NSM`, wrote two tests, and established some of the basic interface, a lot of which was reworked later on.
* [Spatial TODOs](https://github.com/SciML/DiffEqJump.jl/pull/192) -- added optimized versions of `CartesianGrid`, wrote a better test, implemented a more general form of hopping rates.
* [added `reset!` for `PriorityTable`](https://github.com/SciML/DiffEqJump.jl/pull/195) -- small PR adding one function.
* [DirectCRonDirect](https://github.com/SciML/DiffEqJump.jl/pull/197) -- implemented the second spatial SSA -- `DirectCRDirect`.
* [Improved `HopRates`](https://github.com/SciML/DiffEqJump.jl/pull/198) -- optimized `HopRates`.
* [Made `tstops` type-stable in `SSAStepper`](https://github.com/SciML/DiffEqJump.jl/pull/199) -- a small PR fixing a minor type-instability.
* [Cosmetic improvements](https://github.com/SciML/DiffEqJump.jl/pull/201) -- a purely cosmetic PR: renaming things, adding docstrings, etc.
* [Improved `HopRates`](https://github.com/SciML/DiffEqJump.jl/pull/202) -- optimized `HopRates` for `CartesianGrid`.
* [More `HopRates`](https://github.com/SciML/DiffEqJump.jl/pull/203) -- added two new forms of hopping rates to reduce memory use.
* [Rename file](https://github.com/SciML/DiffEqJump.jl/pull/204) -- small PR renaming a file.
* [New `PriorityTable`](https://github.com/SciML/DiffEqJump.jl/pull/208) -- rewrote the `PriorityTable` structure to make it faster. This PR will be merged once it passes review.

I made two pull requests to other `SciML` repositories -- `SciMLTutorials` and `SciMLBenchmarks`.

* [Tutorial for the spatial reversible binding jump model](https://github.com/SciML/SciMLTutorials.jl/pull/430) -- a tutorial showing how to use the newly added functionality in `DiffEqJump`. This was merged.
* [Benchmarking](https://github.com/SciML/SciMLBenchmarks.jl/pull/298) -- a benchmarking script comparing the spatial SSAs. It will be merged once it passes review.
