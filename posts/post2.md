+++
title = "First achievements: Next Subvolume Method"
published = "2021-07-01"
+++

# First achievements: Next Subvolume Method
The past two weeks saw me work on my [first PR](https://github.com/SciML/DiffEqJump.jl/pull/183) this summer, and it is almost done. I implemented the Next Subvolume Method \citep{elf2004spontaneous}, developed an interface for spatial SSAs, which is consistent with the existing interface, and tested the newly implemented SSA.

## Next Subvolume Method
When simulating spatially non-homogeneous systems the standard approach is to split the domain into small sub-domains (often called sites). Each sub-domain is assumed to be well-mixed. Now there are two types of events that can happen at each step of the simulation: reactions within a site and diffusions between neighboring sites.

![alt text](/assets/dna_diffusion_movie_10fps.gif)

The Next Subvolume Method is described in detailed pseudocode in \citep{elf2004spontaneous}.

## References

* \biblabel{elf2004spontaneous}{Elf and Ehrenberg (2004)} Elf, Johan and Ehrenberg, M${\aa}$ns. “Spontaneous separation of bi-stable biochemical systems into spatial domains of opposite phases”. In: _Systems biology_ 1.2 (2004), pp. 230–236.
