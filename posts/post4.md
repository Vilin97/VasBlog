+++
title = "Tutorial on using spatial SSAs in DiffEqJump"
published = "2021-08-02"
+++

# Tutorial on using spatial SSAs in DiffEqJump
This blog will show how to use the functionality I added to `DiffEqJump` over the summer. See [the documentation](https://diffeq.sciml.ai/latest/types/jump_types/) for a tutorial on getting started with `DiffEqJump`.

## Reversible binding model
A 5 by 5 Cartesian grid:

| <!-- -->  | <!-- -->  | <!-- -->  |  <!-- --> | <!-- -->  |
|---|---|---|---|---|
| A,B | . | . | . | . |
| . | . | . | . | . |
| . | . | . | . | . |
| . | . | . | . | . |
| . | . | . | . | C |

Suppose we have a reversible binding system described by $A+B \xleftrightarrow[k_2]{k_1} C$, where $k_1$ is the forward rate and $k_2$ is the backward rate. Further suppose that all $A$ molecules start in the lower left corner, while all $B$ molecules start in the upper right corner of a 5 by 5 grid. There are no $C$ molecules at the start.

We first create the grid:
```julia
using DiffEqJump
dims = (5,5)
grid = DiffEqJump.CartesianGridRej(dims) # or use LightGraphs.grid(dims)
```
Now we set the initial state of the simulation. It has to be a matrix with entry $(s,i)$ being the number of species $s$ at site $i$ (with the standard column-major ordering of the grid).
```julia
starting_state = zeros(Int, 3, 25)
starting_state[1,1] = 25
starting_state[1,2] = 25
```
We now set the time-span of the simulation and the reaction rates
```julia
tspan = (0.0, 2.0)
rates = [3.0, 0.05] # k_1 = rates[1], k_2 = rates[2]
```
Now we can create the `DiscreteProblem`:
```julia
prob = DiscreteProblem(starting_state, tspan, rates)
```
Since both reactions are [massaction reactions](https://en.wikipedia.org/wiki/Law_of_mass_action), we put them together in a `MassActionJump`. In order to do that we create two stoichiometry vectors. The net stoiciometry vector describes which molecules change in number and how much after each reaction; for example, `[1 => -1]` is the first molecule disappearing. The reaction stoiciometry vector describes what the reactants of each reaction are; for example, `[1 => 1, 2 => 1]` would mean that the reactants are one molecule of type 1 and one molecule of type 2.
```julia
netstoch = [[1 => -1, 2 => -1, 3 => 1],[1 => 1, 2 => 1, 3 => -1]]
reactstoch = [[1 => 1, 2 => 1],[3 => 1]]
majumps = MassActionJump(rates, reactstoch, netstoch)
```
The last thing to set up is the hopping constants -- the probability per time of an andividual molecule of each species hopping from one site to another site. In practice this parameter, as well as reaction rates, are obtained empirically. Suppose that molecule $C$ cannot diffuse, while molecules $A$ and $B$ diffuse at probability per time 1 (i.e. the time of the diffusive hop is exponentially distributed with mean 1).
```julia 
hopping_constants = ones(3, 25)
hopping_constants[3, :] = zeros(25)
```
We are now ready to set up the `JumpProblem` with the [next subvolume method](/posts/post2/#nsm). 
```julia
alg = NSM() # Next Subvolume Method. Can also use DirectCRonDirect
jump_prob = JumpProblem(prob, alg, majumps, hopping_constants=hopping_constants, spatial_system = grid, save_positions=(true, false)) 
```
The `save_positions` keyword tells the solver to save the positions just before the jumps. To solve the jump problem do
```julia
solution = solve(jump_prob, SSAStepper())
```
Visualizing solutions of spatial jump problems is best done with animations. 
~~~
<figure>
  <img src="/assets/ABC_anim_275frames_5fps.gif" width="100%" />
</figure>
~~~

This animation was produced by the following script.
```julia
using Plots
is_static(spec) = (spec == 3) # true if spec does not hop
"get frame k"
function get_frame(k, sol, linear_size, labels, title)
    num_species = length(labels)
    h = 1/linear_size
    t = sol.t[k]
    state = sol.u[k]
    xlim=(0,1+3h/2); ylim=(0,1+3h/2);
    plt = plot(xlim=xlim, ylim=ylim, title = "$title, $(round(t, sigdigits=3)) seconds")

    species_seriess_x = [[] for i in 1:num_species]
    species_seriess_y = [[] for i in 1:num_species]
    CI = CartesianIndices((linear_size, linear_size))
    for ci in CartesianIndices(state)
        species, site = Tuple(ci)
        x,y = Tuple(CI[site])
        num_molecules = state[ci]
        sizehint!(species_seriess_x[species], num_molecules)
        sizehint!(species_seriess_y[species], num_molecules)
        if !is_static(species)
            randsx = rand(num_molecules)
            randsy = rand(num_molecules)
        else
            randsx = zeros(num_molecules)
            randsy = zeros(num_molecules)
        end
        for k in 1:num_molecules
            push!(species_seriess_x[species], x*h - h/2 + h*randsx[k])
            push!(species_seriess_y[species], y*h - h/2 + h*randsy[k])
        end
    end
    for species in 1:num_species
        scatter!(plt, species_seriess_x[species], species_seriess_y[species], label = labels[species], marker = 6)
    end
    xticks!(plt, range(xlim...,length = linear_size+1))
    yticks!(plt, range(ylim...,length = linear_size+1))
    xgrid!(plt, 1, 0.7)
    ygrid!(plt, 1, 0.7)
    return plt
end

"make an animation of solution sol in 2 dimensions"
function animate_2d(sol, linear_size; species_labels, title, verbose = true)
    num_frames = length(sol.t)
    anim = @animate for k=1:num_frames
        verbose && println("Making frame $k")
        get_frame(k, sol, linear_size, species_labels, title)
    end
    anim
end
# animate
anim=animate_2d(solution, 5, species_labels = ["A", "B", "C"], title = "A + B <--> C", verbose = false)
fps = 5
name = "ABC_anim_$(length(solution.u))frames_$(fps)fps.gif"
path = joinpath(name)
gif(anim, path, fps = fps)
```