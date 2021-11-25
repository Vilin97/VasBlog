\newcommand{\E}{\mathbb{E}}

# St Petersburg Paradox

Suppose you are offered the following peculiar gamble. You flip a symmetric coin until you get tails. Once you get tails, you get paid $2^n$ dollars, where $n$ is the number of times you flipped the coin (remember that you *must* stop flipping once you get tails). For example, if you get tails on your first flip, you get 2 dollars. If you get tails on your second flip, you get \$4, etc. How much would you be willing to pay to play this game? You would definitely be willing to pay \$2, but what about \$100, \$1000 or a million?

It is probably not a good idea to pay more than \$1000 to play this game, because the chance of getting 10 heads in a row is less than 0.1%. In other words, with probability 99.9% you would lose money. So where is the paradox? Well, you can *expect* to make infinite amount of money from playing this game just once. More precisely, if X is how much money you will be paid (a so-called random variable), the expected value of $X$ is $\frac{1}{2}\cdot 2 + \frac{1}{4}\cdot 4 + \frac{1}{8}\cdot 8 + … = 1 + 1 + 1 + … = \infty$. So in theory you should be willing to pay *any amount*, no matter how large, in order to play this game just once. It is interesting to observe that the expectation is infinite despite the fact that with probability 1 you will receive a finite amount of money.

Let's try to resolve this paradox. Suppose that instead of playing this game once, you can play it as many times as you want (let's disregard how long it would take to flip a coin a billion times) and receive the average of your winnings. Further suppose that it costs $S$ dollars (say, $S$ = 1 million) to play the game, and you get to play it as many times as you choose. But you must choose how many times you will play *before* you start playing; otherwise you could just keep playing until you make enough profit, which seems unfair. To summarize:


* $S$ = the cost of playing the game, e.g. one million

* $N$ = number of times you decide to play the game

* $X_i$ = the (random) amount you win at game i. Since $X_i$ are iid, we just use $X$ when there is no confusion

Now the question becomes: given $S$, how many times $N$ will you play the game to come out positive? How will you go about figuring it out, at least approximately?

The relevant terms from probability theory are Law of Large Numbers and the Central Limit Theorem. But we must be careful using them in our setting. Not only is the second moment of our random variable X infinite, the first moment is as well! Instead we can analyze a truncated random variable $X'$, which agrees with $X$ on the first $2^n$ values, but is equal to 0 for larger values. For example, if $n = 10$, $X'(1) = 2, X'(2) = 4, X'(3) = 8, …, X'(10) = 2^{10}, X'(11) = 0 = X'(12) = \dots$. So $X'$ is an underestimate of the amount of money you actually win. Intuitively, $X'$ makes a lot of sense: if the probability of flipping over 10 heads in a row is less than 0.1%, we may as well treat it as zero, and just pretend that it will never happen. It is convenient that the expected value of $X'$ is exactly n, instead of infinity. Now that we got our hands on a simpler random variable, let's see what will happen to the average of $X'_1, …., X'_N$.

Let $\bar X' = 1/M\cdot \sum_{i=1}^N X'_i$ be the average of our new random variables $X'_1, …., X'_N$. Since $X'$ are all iid (independent and identically distributed), the expected value of $\bar X'$ is n as well. Also, the variance of $\bar X'$ is $\frac{1}{M} \cdot (\text{variance of }X')$. The variance of X is $\E[X'^2] - \E[X']^2 = 2^{n+1} – 1 – n^2 \approx 2^{n+1}$. So The variance of $\bar X'$ is approximately $2^{n+1}/M$. 

Let's pause here to ponder what we just computed. Since we only use n to simplify our analysis, we are free to choose it any way we like. Increasing n increases the expected value for $X'$ but it also increases its variance exponentially! Recall that variance is a measure of how sure we can be that $X'$ lands close to its expected value. So there seems to be a trade-off between higher expected value and being confident that we are not too far off from the expected value.

Now we will play fast and loose with the Central Limit Theorem by treating $\bar X'$ as a normal random variable (it is not necessary but will simplify the analysis). Since it costs S dollars to play the game, we want the expected value of $\bar X'$ to be at least S. So let's take $n$ to be $2S$. Now the variance of $\bar X'$ is $2^{2S+1}/M$. Now taking $M$ to be $2^{2S+1} = 2 \cdot (2^S)^2$ ensures that the variance is 1. Since we are treating $\bar X'$ as a normal random variable, the probability that $\bar X'$ is below $S$ is *very low*. Since $X'$ is an underestimate of $X$, $\bar X'$ is an underestimate of the average of $X_1, …, X_M$. So with overwhelming probability, the average of your winnings will be above $S$.

We have now answered the question: taking $M$ to be $2 \cdot (2^S)^2$ ensures coming out positive when the cost is $S$. The important bit here is that $M$ is exponential in $S$. For example, if $S = 10$, i.e. it costs \$10 to play, then our computations suggest $M = 2 \cdot 2^{20} \approx 1,000,000$. This seems like a gross over-estimate. Perhaps using finer techniques such as tail bounds on subgaussian random variables will yield a more realistic estimate.
