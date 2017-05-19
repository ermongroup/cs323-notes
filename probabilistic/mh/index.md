---
layout: post
title: 'Metropolis-Hastings'
---

# Recap of Markov chains

In the last lecture, we learned that if a Markov chain is irreducible
and aperiodic, then the Markov chain will converge to its unique
stationary distribution, regardless of the initial state.
Mathematically, $$\lim_{n\to\infty}P^{n}=W$$, a matrix $$W$$ where every row
is equal to some vector $$w$$.

Recall that $$P$$ is a stochastic matrix. $$P$$ has a left eigenvector $$w$$
with eigenvalue 1. $$P$$ has no other eigenvectors with modulus 1. Let
$$\{v_{i}\}$$ be a basis of eigenvectors for $$P$$ where each $$v_{i}$$
satisfies $$v_{i}P={\lambda}_{i}v_{i}$$. When we start with an initial
$$\Pi^{0}=\sum_{i}{\alpha}_{i}v_{i}$$, then
$$\Pi^{0}P=\sum_{i}{\alpha}_{i}v_{i}P=\sum_{i}{\alpha}_{i}{\lambda}_{i}v_{i}$$
and
$$\Pi^{0}P^{n}=\sum_{i}{\alpha}_{i}{\left({\lambda}_{i}\right)}^{n}v_{i}$$,
and $${\left({\lambda}_{i}\right)}^{n}\to0$$ for the eigenvalues
corresponding to all eigenvectors except $$w$$. The speed at which we have
convergence is determined by the size of the second-largest eigenvalue.

Our proof of the theorem last time was based on the fact that every time
we apply $$P$$, we decrease the gap between the smallest and largest value
of the state vector, and hence the state vector converges to a constant
vector. Note that if $$d$$ is larger, then the Markov chain will converge
more quickly. By the definition of $$d$$, the Markov chain will converge
more quickly if there is no pair of states with a very low probability
of transitioning between the states.

# Markov Chain Monte Carlo (MCMC)

Our goal in Markov Chain Monte Carlo (MCMC) is to sample from a
probability distribution
$$p(x)=\frac{1}{Z}w(x)=\frac{1}{Z}\prod_{c}\phi_{c}(x)$$. We want to
construct a Markov chain that reaches the limiting distribution $$p(x)$$
as fast as possible. The main question is how do we design a transition
matrix $$P$$ so that $$p(x)$$ is a left eigenvector of $$P$$ with eigenvalue
1.

Note that in the high-dimensional case, we cannot even store all of the
entries of $$w(x)$$. We will only assume we can evaluate
$$w(x)=\prod_{c}\phi_{c}(x)$$ for any $$x$$. This also means that we do not
need to calculate the normalization constant $$Z$$.

The key assumption we will make is that the Markov chain is reversible.
A Markov chain is reversible if there exists a distribution $$\Pi^{*}$$
which satisfies the detailed balance conditions: $$\forall i,j$$,
$$\Pi_{i}^{*}P_{ij}=\Pi_{j}^{*}P_{ji}$$.

**Theorem**: If a distribution $$\Pi^{*}$$ is reversible, then $$\Pi^{*}$$
is a stationary distribution.

***Proof***: For any state $$j$$, we have 

\$$
\begin{eqnarray*}
\sum_{i}\Pi_{i}^{*}P_{ij} & = & \sum_{i}\Pi_{j}^{*}P_{ji}\\
\sum_{i}\Pi_{i}^{*}P_{ij} & = & \Pi_{j}^{*}
\end{eqnarray*}
\$$
{: style="text-align: center"}

Therefore, $$\Pi^{*}P=\Pi^{*}$$.

Since we want the stationary distribution of the Markov chain to be
$$p(x)$$, it suffices to design the transition matrix $$P$$ so the Markov
chain satisfies detailed balance with respect to $$p(x)$$.

# Metropolis-Hastings

Metropolis-Hastings is a MCMC method for sampling from a probability
distribution $$p(x)=\frac{1}{Z}w(x)=\frac{1}{Z}\prod_{c}\phi_{c}(x)$$ by
using a proposal distribution for proposing moves and then accepting or
rejecting proposed moves between states with some probability.

First, let $$Q$$ be any proposal distribution where $$q(i,j)=Q(j\mid i)$$ is
the probability of proposing a move to some state $$j$$ given the current
state $$i$$. Then we will construct the transition matrix $$P$$ as


\$$
\begin{eqnarray*}
P_{ij} & = & P(X_{n}=j\mid X_{n-1}=i)=\begin{cases}
q(i,j){\alpha}(i,j) & \text{if }j\ne i\\
q(i,i)+\sum_{k\ne i}q(i,k)(1-{\alpha}(i,k)) & \text{otherwise}
\end{cases}\\
\end{eqnarray*}
\$$
{: style="text-align: center"}

where
$${\alpha}(i,j)=\min\bigg\{1,\frac{p(j)q(j,i)}{p(i)q(i,j)}\bigg\}=\min\bigg\{1,\frac{w(j)q(j,i)}{w(i)q(i,j)}\bigg\}$$
is the acceptance probability for accepting a proposed move from state
$$i$$ to state $$j$$. Note that while we cannot evaluate $$p(x)$$ exactly, we
can evaluate ratios $$p(j)/p(i)$$.

We want to show that $$p$$ satisfies detailed balance for all $$i,j$$. By
the definition of $${\alpha}$$, without loss of generality, assume that
$${\alpha}(j,i)=1$$ and $${\alpha}(i,j)=\frac{w(j)q(j,i)}{w(i)q(i,j)}$$.
Then 

\$$
\begin{eqnarray*}
w(i)q(i,j)\frac{w(j)q(j,i)}{w(i)q(i,j)} & = & w(j)q(j,i)\\
w(i)q(i,j){\alpha}(i,j) & = & w(j)q(j,i){\alpha}(j,i)\\
p(i)P_{ij} & = & p(j)P_{ji}
\end{eqnarray*}
\$$
{: style="text-align: center"}

Therefore, $$p$$ satisfies detailed balance and is a stationary
distribution.

Now we just need to ensure that the Markov chain is irreducible and
aperiodic. This depends on our choice of the proposal distribution $$Q$$
and target probability distribution $$p$$ since $$p(i)q(i,j){\alpha}(i,j)$$
defines the probability of transitioning from $$i\to j$$.

The intuition behind MH is that sampling from the Markov chain requires
spending the right amount of time in the right states so that our
samples are accurate draws. We need to balance the transitions between
higher probability states and lower probability states under $$p$$ with
the tendency of the proposal distribution to go to certain states. Given
$$p$$ and $$Q$$, MH will set $${\alpha}$$ so $$p$$ satisfies detailed balance.

# Metropolis-Hastings algorithm

The procedure for the Metropolis-Hastings algorithm is as follows:
1.  Initialize $$x^{(t)}$$ for $$t=0$$
2.  Draw a sample $$x'$$ from $$Q(x'\mid x^{(t)})$$
3.  Accept the move with probability
    $$A(x'\mid x^{(t)})=\min(1,{\alpha})$$ where
    $${\alpha}=\frac{p(x')Q(x^{(t)}\mid x')}{p(x^{(t)})Q(x'\mid x^{(t)})}$$.
    If accepted, let $$x^{(t+1)}=x'$$. Otherwise, let $$x^{(t+1)}=x^{(t)}$$.
4.  Repeat steps 2-3 to draw samples

In practice, we run a burn-in period of $$T$$ iterations and start
collecting samples only after the burn-in period and hope that the
Markov chain has converged by then. However, determining when the Markov
chain has converged is a hard problem. One heuristic is to randomly
initialize several Markov chains, plot some scalar function of the state
of the Markov chain over time, and see if the scalar functions are
similar. Note that this does not always guarantee convergence. For
example, consider the case where we have a bimodal distribution or a
singly peaked distribution.

The samples drawn from the Markov chain are not i.i.d. In general,
samples drawn close to each other can be highly correlated since
Metropolis-Hastings moves tend to be local moves. However
asymptotically, the samples drawn from the Markov chain are all unbiased
and all come from the right distribution. The variance will not decrease
as fast as if we had independent samples though.
