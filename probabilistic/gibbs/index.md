---
layout: post
title: Gibbs sampling
---

# Sampling and inference tasks

In sampling, we are concerned with how to sample from a target
probability distribution $$p(x)=\frac{w(x)}{Z}$$. Given samples
$$x_{1},...,x_{T}\sim p(x)$$, we can express a quantity of interest as the
expected value of a random variable and then use the estimator
$$\frac{1}{T}\sum_{t=1}^{T}\phi(x_{t})$$ to estimate
$$\mathbb{E}_{p(x)}[\phi(x)]=\sum_{x}p(x)\phi(x)$$ . For example, to
estimate the marginal probability $$P(x\left[1\right]=T)$$, we let
$$\phi(x)={\mathbb{I}\left[{x\left[1\right]=T}\right]}$$. Thus, we see
that we can use a MCMC algorithm to draw samples from $$p(x)$$ and then
use the samples to estimate quantities of interest.

# Gibbs sampling

Last lecture, we described the Metropolis-Hastings algorithm for
sampling from a probability distribution
$$p(x)=\frac{1}{Z}w(x)=\frac{1}{Z}\prod_{c}\phi_{c}(x)$$ by using a
proposal distribution and acceptance probabilities for accepting
proposed moves. The MH algorithm only required computing the ratios
$$p(x')/p(x)$$. Note that the MH algorithm treats $$p(x)$$ as a black box
and does not leverage any particular structure of $$p(x)$$. Similarly, the
proposal distribution we choose typically does not depend on $$p(x)$$ and
also does not leverage any structure of $$p(x)$$.

Gibbs sampling is a MCMC algorithm that repeatedly samples from the
conditional distribution of one variable of the target distribution $$p$$,
given all of the other variables. Gibbs sampling works as follows:
1.  Initialize $$x^{(t)}={\left(x_{1}^{(t)},...,x_{k}^{(t)}\right)}$$ for
    $$t=0$$
2.  For $$t=0,1,...$$
    1.  Pick index $$i$$ uniformly at random from $$1,...,k$$
    2.  Draw a sample $$a\sim p(x_{i}'\mid x_{-i}^{(t)})$$ where
        $$x_{-i}^{(t)}$$ is the set of all variables in $$x^{(t)}$$ except
        for the $$i^{th}$$ variable.
    3.  Let
        $$x^{(t+1)}=(x_{1}^{(t)},x_{2}^{(t)},...,x_{i-1}^{(t)},a,x_{i+1}^{(t)},...,x_{k}^{(t)})$$.

Gibbs sampling assumes we can compute conditional distributions of one
variable conditioned on all of the other variables and sample exactly
from these distributions. In graphical models, the conditional
distribution of some variable only depends on the variables in the
Markov blanket of that node.

Let us now show that Gibbs sampling is a special case of
Metropolis-Hastings where the proposed moves are always accepted (the
acceptance probability is 1).

Let $$x_{i}$$ denote the $$i^{th}$$ variable, and let $$x_{-i}$$ denote the
set of all variables except $$x_{i}$$. Let
$$Q(x_{i}',x_{-i}\mid x_{i},x_{-i})=\frac{1}{k}p(x_{i}'\mid x_{-i})$$. Let
$$A(x_{i}',x_{-i}\mid x_{i},x_{-i})=\min(1,{\alpha})$$ where

\$$
\begin{eqnarray*}
{\alpha}& = & \frac{p(x_{i}',x_{-i})Q(x_{i},x_{-i}\mid x_{i}',x_{-i})}{p(x_{i},x_{-i})Q(x_{i}',x_{-i}\mid x_{i},x_{-i})}\\
 & = & \frac{p(x_{i}',x_{-i})p(x_{i}\mid x_{-i})}{p(x_{i},x_{-i})p(x_{i}'\mid x_{-i})}\\
 & = & \frac{p(x_{i}'\mid x_{-i})p(x_{-i})p(x_{i}\mid x_{-i})}{p(x_{i}\mid x_{-i})p(x_{-i})p(x_{i}'\mid x_{-i})}\\
 & = & 1
\end{eqnarray*}
\$$
{: style="text-align: center"}

Gibbs sampling is used very often in practice since we don’t have to
design a proposal distribution. Note that the Gibbs sampling algorithm
described earlier is known as random scan Gibbs sampling because we
choose an index uniformly at random in each iteration. A common
implementation of Gibbs sampling is systematic scan Gibbs sampling where
we have a for loop that goes through all of the variables $$x_{i}$$ in
some order and samples $$x_{i}^{(t+1)}$$ from the conditional distribution
of $$x_{i}'$$ given all of the other variables.

# Variants of Gibbs sampling

One variant of Gibbs sampling is blocked Gibbs sampling, where we group
two or more variables together in a block and update this block by
sampling from the joint distribution of these variables conditioned on
all of the other variables. Updating more variables at a time in blocks
is helpful. Consider the limiting case where if we could sample from a
block containing all of the variables, then we could sample directly
from $$p(x)$$.

Another variant is collapsed Gibbs sampling. In this algorithm, we
marginalize out as many variables as possible before sampling from the
conditional distribution of some variable.

As an example, suppose the target probability distribution we want to
sample from is $$p(x,y\mid z)$$.

In Gibbs sampling, we would alternately sample
$$x^{(t+1)}\sim p(x\mid y^{(t)},z)$$ and
$$y^{(t+1)}\sim p(y\mid x^{(t+1)},z)$$.

In collapsed Gibbs sampling, we would alternately sample
$$y^{(t+1)}\sim p(y\mid z)$$ and then
$$x^{(t+1)}\sim p(x\mid y^{(t+1)},z)$$. Note that in this case, we are
drawing samples from the exact distribution
$$p(x,y\mid z)=p(x\mid y,z)p(y\mid z)$$.

Note that the ordering of the variables in the sampling procedure is
very important for collapsed Gibbs sampling (to ensure that the
resulting Markov chain has the right stationary distribution) since the
right ordering might depend on which variables we marginalize out.

# Simulated annealing

Simulated annealing is an adaptation of the Metropolis-Hastings
algorithm and is a heuristic for finding the global maximum of a given
function. Consider the case of SAT where we are given a CNF formula and
want to find a satisfying assignment. Simulated annealing moves around
the space trying to find assignments that satisfy as many clauses as
possible.

We construct a probability distribution that puts high probability on
assignments that satisfy many clauses. Let
$$p({\sigma})=\frac{\exp{\left(E({\sigma})/T\right)}}{Z}$$ where
$$E({\sigma})$$ is the number of clauses satisfied by $${\sigma}$$ and $$T$$
is the “temperature” parameter. As $$T\to\infty$$, $$p({\sigma})$$
approaches the uniform distribution. As $$T\to0$$, $$p({\sigma})$$ tends to
put all of the probability mass on satisfying assignments.

To solve the optimization problem, we want to sample from this
distribution when $$T$$ is small. We can use Metropolis-Hastings with a
proposal distribution that randomly picks a variable and flips it. Let
$$Q(x'\mid x)$$ be equal to $$1/n$$ if $$x,x'$$ differ in only one variable
and $$0$$ otherwise. The Metropolis-Hastings acceptance probability is
$$A(x'\mid x)=\min{\left(1,\frac{p(x')}{p(x)}\right)}=\min{\left(1,\exp{\left(\frac{E({\sigma}')-E({\sigma})}{T}\right)}\right)}$$.
If $$E({\sigma}')\geq E({\sigma})$$, then we always accept the transition.
$$T$$ controls how greedy we are.

The algorithm starts with a large $$T$$ in order to move around the space
freely and decreases $$T$$ slowly to concentrate probability mass on the
states which satisfy the most clauses. $$T$$ is decreased based on some
annealing schedule. Each iteration is otherwise a MH update.
