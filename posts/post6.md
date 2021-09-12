+++
title = "A puzzle"
published = "2021-09-12"
+++

\newcommand{\O}{\mathcal O}
\newcommand{\ld}{\underline{d}}
\newcommand{\e}{\epsilon}

\toc

# A puzzle

The other day my friend Leo presented the following puzzle to me. Given a partition $\set{\O_n}_{n\in I}$ of positive integers with $\O_n$ all infinite, find a condition that is equivalent to $I$ finite. The motivation for the problem was juggling: each $\O_n$ is the "orbit" of the $n^{\text{th}}$ ball, and we want to know when we are juggling finitely many balls.

## Mathematical observations

### Width and density

After playing around with some examples we came up with the definition of width of an infinite subset of $\Z_+$: the width of $\O$ denoted by $w(\O)$ is the supremum of distances between consecutive elements. Intuitively, width is kind of the reciprocal of [natural density](https://en.wikipedia.org/wiki/Natural_density). Recall that the natural density of $\O$ is the limit $d(\O) = \lim_{n\to \infty} a(n)/n$ where $a(n)$ is the number of elements of $\O$ in $\set{1,...,n}$. Therefore $1/w(\O) \leq d(\O)$. Let's see why that is.

Given $\O = \set{x_i}_1^\infty \subset \Z_+$, let $y_0 = x_1$ and $y_i = x_{i+1} - x_i$ so that $y_i$'s are the "gaps" between consecutive terms of the sequence $\O$. Then $w(\O) = \sup \set{y_i: i\geq 1}$. On the other hand, for $n = x_i$
\begin{align}\label{eq: density and width}
    a(n)/n &= i/x_i \text{\hspace{2cm} because $a(x_i) = i$}\\
    &= \frac{i}{\sum_{j=1}^i y_j} \geq \frac{i}{\sum_{j=1}^i w(\O)} = 1/w(\O).
\end{align}
So taking $\lim_i \to \infty$ we get $d(\O) \geq 1/w(\O)$. Additionally, if all but finitely many gaps $y_i$ are equal to $w(\O)$, then $d(\O)$ exists and equals $1/w(\O)$.

Taking $\liminf_{i\to\infty}$ of \eqref{eq: density and width} actually shows that even the lower density $\underline{d}(\O) = \liminf_n a(n)/n$ is at least $1/w(\O)$. The advantage of using the lower density is that it is defined for all subsets of natural numbers. Let's record this observation.

*$O_1$*: for $\O \subset \Z_+$, $1/w(\O) \leq \ld(\O)$, where $1/w(\O)=0$ if $w(\O) = \infty$.

### Growth of $w(\O_n)$

Here is another easy observation:

*$O_2$*: If $\forall n \in I, w(\O_n) \leq M$ for some fixed integer $M$, then $I$ is finite.

*Proof*: If $I$ is infinite, consider $M+1$ sets $\O_1,...,\O_{M+1}$ and an interval $[a,a+M-1]$ where $a$ is large enough that the smallest element of each $\O_n, n\leq M+1$ is below $a$. Then all $M+1$ disjoint sets $\O_1,...,\O_{M+1}$ have at least one element in $[a,a+M-1]$, which is impossible by pigeonhole principle.

So if $w(\O_n) = O(1)$, then $I$ is finite -- we are juggling finitely many balls. This leads to another conjecture: if $\forall n \in I, w(\O_n) \leq M_n$, then $I$ is finite (the only difference is that now $M_n$ depends on $n$). This one is not true. Let the first set fill half of naturals, the second -- half of the remaining half, etc:
$$\O_1 = 2\Z_+,\ \O_2 = 1 + 4\Z_+,\ \O_3 = 3+8\Z_+,\ ... ,\ \O_n = 2^{n-1}-1 + 2^n\Z_+.$$
Here $w(\O_n) = 2^n$ with $I$ infinite.

So far we know that if $w(\O_n) = O(1)$, then $I$ is finite, but $w(\O_n) = O(2^n)$ is not strong enough to guarantee that. A natural question is:

*Question*: is there an increasing function $f(n)$ such that $w(\O_n) = O(f(n))$ if and only if $I$ is finite? Perhaps $f(n) = n^2$ or another polynomial?

We already saw that $1/w(\O) \leq \ld(\O)$. So it might be fruitful to think about the (lower) densities of $\O_n$. Intuitively, the sum of densities of a partition cannot be larger than 1 because each density is the "proportion" of the set in $\Z_+$. Indeed, this is easy to formally state and prove:

*$O_3$*: if sets $\set{\O_n}_{n=1}^\infty$ are disjoint, then $\sum_{n=1}^\infty \ld(\O_n) \leq 1$.

*Proof*: It suffices to prove that $\sum_{n=1}^N \ld(\O_n) \leq 1$ for all $N$ so fix any $N$. We have

\begin{align}
    \sum_{n=1}^N \ld(\O_n) &= \liminf_{i\to\infty} \sum_{n=1}^N \frac{|\O_n\cap [i]|}{i} = \liminf_{i\to\infty} \frac{1}{i}\sum_{n=1}^N |\O_n\cap [i]|\\
    &\leq  \liminf_{i\to\infty} \frac{1}{i}\times i = 1 \text{ \hspace{1cm} because $\O_n$'s are disjoint}.
\end{align}

Putting observations *$O_1$* and *$O_3$* together we obtain

*$O_4$*: If $I$ is infinite, then $\sum_{n=1}^\infty 1/w(\O_n) \leq 1$. In particular, $1/w(\O_n)$ is asymptotically smaller than $1/n$. Formally, $w(\O_n) = \omega(n)$.

The contrapositive of the above is $w(\O_n) = O(n) \implies |I| < \infty$. Is the converse true? For example, can we find an infinite partition $\set{\O_n}$ of $\Z_+$ with $w(\O_n) \leq n^2?$ In order to answer this question I wrote a [program](https://github.com/Vilin97/Math/blob/master/src/partition_integers.jl) that explores the "greedy" algorithm of making successive sets $\O_n$.

## Programming exploration

### Greedy approach
We want to see if we can "fit" infinitely many disjoint sets $\set{\O_n}$ in $\Z_+$ with $w(\O_n) \leq n^2$ (starting at $n=2$). The naive greedy algorithm for doing this is as follows. Let $\O_2 = 4,8,12,16,\text{etc}$. We now want to let $\O_3 = 9,18,27,36,\text{etc.}$ but there is a collision at $36$. So change $36$ to $35$ and keep going at strides of $9$. In general, place the next element of $\O_n$, go to the current element plus the width $n^2$. If this spot is empty, put it there, if not, try the spot one below, etc. If we are ever unable to find the next spot because on our search down we returned back to the current element, then this strategy fails. If no such failure occurs, then we succeed.

The aforementioned [program](https://github.com/Vilin97/Math/blob/master/src/partition_integers.jl) successfully uses this strategy for $10^8$ numbers, i.e. we fill the set $\set{1,\dots,10^8}$ with sets $\O_2,\dots,\O_{\sqrt{10^8}}$ without any problems. Additionally letting the width of $\O_n$ be $c \cdot n^{1+\e}$ for higher constants $c$ and smaller values of $\e>0$ suggests that this strategy is successful for $w(\O_n) = \Theta(n^{1+\e})$ for $\e>0$ arbitrarily small. So we have the conjecture

*$C_1$*: For any $\e>0$ the "greedy" algorithm produces infinitely many sets $\O_n$ with $w(\O_n) = \Theta(n^{1+\e})$.

If this conjecture is true, then we have the following two facts:

1. $|I| = \infty \implies w(\O_n) = \omega(n)$.
2. $w(\O_n) = \Omega(n^{1+\e}) \implies |I| = \infty$.

At that point it is still unclear what happens at, say, $w(\O_n) = \Theta(n\log^2 n)$, since $\sum \frac{1}{n\log^k n}$ converges for $k>1$. But if *$C_1$* is true, perhaps the following is true as well.

*$C_2$*: For any increasing function $f$ such that $\sum_n 1/f(n)$ converges, the "greedy" algorithm produces infinitely many sets $\O_n$ with $w(\O_n) = \Theta(f(n))$.

This would finally give the equivalence 
$$|I|=\infty \iff \sum_{n\in I} 1/w(\O_n) < \infty.$$
If $I$ is finite then any sum over $n\in I$ converges, so stating the statement above takes a bit more precision but we'll cross that bridge when we get there.

### Formalization in Lean

[Lean](https://leanprover-community.github.io/) is a proof assistant that allows people to "explain" mathematics to a computer. For example, here is my [proof](https://github.com/Vilin97/tutorials/blob/master/src/my_exercises/05_sequence_limits.lean#L168) of the squeeze theorem. It would be cool to formalize the above observations in Lean.