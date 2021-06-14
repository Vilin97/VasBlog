@def title = "Blog"
@def hascode = true
@def rss = "Blog of Vasily Ilin"
@def rss_title = "VasBlog"
@def rss_pubdate = Date(2021, 6, 11)


# Blog

\toc

## First post: what I'm doing in summer 2021
<!-- add links to project, people, etc -->
I am happy to have been accepted to Google Summer of Code 2021 with the [project](https://summerofcode.withgoogle.com/projects/#5463862406545408) "Efficient Spatial Simulations in DiffEqJump." I will be writing code under the mentorship of [Samuel Isaacson](http://math.bu.edu/people/isaacson/) and [Chris Rackauckas](https://chrisrackauckas.com/). The goal of the project is to expand [DiffEqJump](https://github.com/SciML/DiffEqJump.jl) with new, optimized spatial solvers and a consistent interface, enabling the study of large spatial systems of jump processes.

### Synopsis
Jump processes are a fundamental component in stochastic models throughout engineering, medicine and the sciences. For example, biology is rich with chemical networks consisting of hundreds or thousands of chemical reactions such as the B-cell network \citep{barua2012computational}. Often they are modelled as systems of differential equations, which has multiple drawbacks:
* in general the solutions to the DE's do not coincide with the mean of the stochastic solutions;
* variance and other moments cannot be gathered from solutions to DE's;
* when the number of species (e.g. molecules or individuals) is low, treating it as a continuous random variable introduces inaccuracies.

Stochastic Simulation Algorithms (SSAs) are used to stochastically simulate the system. While there are approximate SSAs, the focus of this project is on exact SSAs -- those that keep all statistics exact modulo the sampling error.

DiffEqJump is a part of the SciML ecosystem, which contains infrastructure to perform such simulations. DiffEqJump is a package in [Julia](https://julialang.org/), and is a part of the [SciML](https://sciml.ai/) ecosystem. It contains infrastructure to perform such simulations.

While SSAs for spatially non-homogeneous systems exist in academic literature \citep{elf2004spontaneous, sanft2015constant}, they are not currently supported in DiffEqJump. The goal of this project is to expand DiffEqJump with new, optimized spatial solvers and a consistent interface,
enabling the study of large spatial systems of jump processes and the use of jump processes within other SciML tooling.

### My motivation
\newcommand{\arrow}{\xrightarrow}

@@row
@@container
@@right
The SIR model
\begin{align} \label{eqn: SIR model}
  S + I &\arrow{\alpha} 2I\\  
  I &\arrow{\beta} R
\end{align}
@@
@@
@@

My main motivation for working on DiffEqJump is to take part in the effort of building better models to describe the world around us. DiffEqJump is used by scientists in the fields of biology, medicine, applied mathematics to stochastically simulate chemical and other jump process-based network models. One application of a non-chemical network, whose evolution can be stochastically simulated, is epidemic dynamics, the simplest of which is the SIR model \eqref{eqn: SIR model} (SIR stands for Susceptible, Infected and Recovered/Removed). Since the assumption of people being well-mixed is violated in this scenario, the model would be more realistic if people were allowed to "diffuse" between different locales, and "react" when they are within the same locale. As the year of 2020 showed, epidemic dynamics is one of the most important applications to which mathematical models can provide critical insights.

Another big motivation for working on DiffEqJump comes from knowing that it is freely available for any modeler, for example, being actively used by researchers in Quantitative Systems Pharmacology working on drug development. This open source project helps biologists, chemists, epidemiologists and other scientists simulate stochastic networks efficiently and accurately.


* \biblabel{barua2012computational}{Barua et al. (2012)} Dipak Barua, William S Hlavacek, and Tomasz Lipniacki. “A computational model for early events in B cell antigen receptor signaling: analysis of the roles of Lyn and Fyn”. In: _The Journal of Immunology_ 189.2 (2012),pp. 646–658.
* \biblabel{elf2004spontaneous}{Elf & Ehrenberg (2004)} Johan Elf and M ̊ans Ehrenberg. “Spontaneous separation of bi-stable biochemical systems into spatial domains of opposite phases”. In: _Systems biology_ 1.2 (2004), pp. 230–236.
* \biblabel{sanft2015constant}{Sanft & Othmer (2015)}  Kevin  R  Sanft  and  Hans  G  Othmer.  “Constant-complexity  stochastic  simulation  algorithm  with  optimal binning”. In: _The Journal of chemical physics_ 143.7 (2015), 08B6091.
