+++
title = "About Me"
hascode = true
date = Date(2021, 6, 11)
rss = "About Vasily Ilin"
+++
<!-- @def tags = ["syntax", "code"] -->

# About me

\toc

## Background
I grew up in Moscow, Russia. At 17 I moved to Boston to attend Boston University. My first academic interest was economics but I switched to math mid-way. In 2017 I spent a semester in Auckland, New Zealand, where I took my first proof based-math class. In 2020 I graduated from BU but stayed another year for a Masters degree in computer science, graduating in 2021. I've been doing a PhD in pure mathematics at the University of Washington since then.

## Professional interests
<!-- TODO: add links to projects -->
On the math side of things I enjoy anything interdisciplinary. For example, using algebra to study topological spaces (shoutout to $\pi_1$ and $H_{\text{dR}}$) or using smooth manifold theory in complex analysis. I do not have a concrete research interest yet; working on it!

On the computer science side of things, my favorite topic is computational complexity from both the theoretical and practical points of view. I like the process of designing fast algorithms and implementing them efficiently.

## Personal
<!-- TODO: add refs, photos -->
In my free time I do rock climbing (mostly indoor bouldering) and calisthenics (hand stands, etc). I also like reading popular science books: some my favorites are "How to Hide an Empire" by (**author?**) and "The Sixth Extinction" by (**author?**). I use GoodReads (**link?**) to keep track of books I want to read and to review some.

## Live evaluation of code blocks

If you would like to show code as well as what the code outputs, you only need to specify where the script corresponding to the code block will be saved.

Indeed, what happens is that the code block gets saved as a script which then gets executed.
This also allows for that block to not be re-executed every time you change something _else_ on the page.

Here's a simple example (change values in `a` to see the results being live updated):

```julia:./exdot.jl
using LinearAlgebra
a = [1, 2, 3, 3, 4, 5, 2, 2]
@show dot(a, a)
println(dot(a, a))
```

You can now show what this would look like:

\output{./exdot.jl}

**Notes**:
* you don't have to specify the `.jl` (see below),
* you do need to explicitly use print statements or `@show` for things to show, so just leaving a variable at the end like you would in the REPL will show nothing,
* only Julia code blocks are supported at the moment, there may be a support for scripting languages like `R` or `python` in the future,
* the way you specify the path is important; see [the docs](https://tlienart.github.io/franklindocs/code/index.html#more_on_paths) for more info. If you don't care about how things are structured in your `/assets/` folder, just use `./scriptname.jl`. If you want things to be grouped, use `./group/scriptname.jl`. For more involved uses, see the docs.

Lastly, it's important to realise that if you don't change the content of the code, then that code will only be executed _once_ even if you make multiple changes to the text around it.

Here's another example,

```julia:./code/ex2
for i ∈ 1:5, j ∈ 1:5
    print(" ", rpad("*"^i,5), lpad("*"^(6-i),5), j==5 ? "\n" : " "^4)
end
```

which gives the (utterly useless):

\output{./code/ex2}

note the absence of `.jl`, it's inferred.

You can also hide lines (that will be executed nonetheless):

```julia:./code/ex3
using Random
Random.seed!(1) # hide
@show randn(2)
```

\output{./code/ex3}


## Including scripts

Another approach is to include the content of a script that has already been executed.
This can be an alternative to the description above if you'd like to only run the code once because it's particularly slow or because it's not Julia code.
For this you can use the `\input` command specifying which language it should be tagged as:


\input{julia}{/_assets/scripts/script1.jl} <!--_-->


these scripts can be run in such a way that their output is also saved to file, see `scripts/generate_results.jl` for instance, and you can then also input the results:

\output{/_assets/scripts/script1.jl} <!--_-->

which is convenient if you're presenting code.

**Note**: paths specification matters, see [the docs](https://tlienart.github.io/franklindocs/code/index.html#more_on_paths) for details.

Using this approach with the `generate_results.jl` file also makes sure that all the code on your website works and that all results match the code which makes maintenance easier.
