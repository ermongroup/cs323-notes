---
layout: post
title: Markov chains
---

# Markov Chain Monte Carlo (MCMC)

Suppose we want to sample from a complex probability distribution that
can be factorized as
$$p(x)=\frac{1}{Z}\prod_{c\in\mathcal{C}}\phi_{c}(x)$$ where
$$Z=\sum_{x\in\{0,1\}^{n}}\prod_{c\in\mathcal{C}}\phi_{c}(x)$$.

Recall the random walk algorithm that we discussed for deciding
satisfiability for SAT, where we moved along the edges of the Boolean
hypercube looking for a satisfying assignment by picking any violated
clause and flipping the truth assignment of a randomly chosen variable
in that clause to make that clause satisfiable.

We will present a random walk Monte Carlo method which moves around the
Boolean hypercube in a special way such that the random walk will visit
points with a frequency proportional to the corresponding probability
$$p(x)$$. In other words, we will spend more time in areas assigned higher
probability by the model, and as such, we will draw more samples $$x$$
wherever $$p(x)$$ is high. To do so, we will construct a Markov chain
whose limiting distribution is the desired target distribution $$p(x)$$.
More specifically, we want to design the transition matrix of the Markov
chain so simulating the Markov chain for a sufficiently large number of
steps will give us samples from $$p(x)$$. We will prove that, under some
conditions, this property will hold no matter how we initialize the
Markov chain (regardless of which state we start in).

# Markov chains - introduction

Let the state space $$S=\{0,1\}^{n}$$ be the set of vertices of the
Boolean hypercube. Since the number of possible states is $$2^{n}$$, for
large $$n$$, the state space cannot be enumerated completely, and the
transition matrix $$P\in{\mathbb{R}}^{|S|\times|S|}$$ cannot be
represented explicitly. We will to find a way to simulate from the
Markov chain without representing the Markov chain explicitly.

The Markov chain is completely defined by the initial state $$\Pi^{0}$$
and the transition matrix $$P=[p_{ij}]$$, where $$p_{ij}$$ is the
probability of transitioning from state $$i$$ to $$j$$, since
$$\Pi^{1}=\Pi^{0}P$$ and $$\Pi^{2}=\Pi^{1}P=\Pi^{0}P^{2}$$ and more
generally $$\Pi^{k}=\Pi^{0}P^{k}$$. For example, the initial distribution
can be uniform,
$$\Pi^{0}={\left(\frac{1}{2^{n}},\frac{1}{2^{n}},\ldots,\frac{1}{2^{n}}\right)}$$,
or initialized in a single state,
$$\Pi^{0}={\left(0,\ldots,0,1,0,\ldots,0\right)}$$.

Our goal is to design the transition matrix so that, regardless of the
initial state $$\Pi^{0}$$, we will have $$\Pi^{0}P^{T}$$ converge to the
target probability distribution $$p(x)$$ after a sufficiently long time.

Let us first look at the eigenvectors of $$P$$, any vector $$v$$ satisfying
$$vP={\lambda}v$$ for some $${\lambda}$$. If we have a basis of eigenvectors
$$v_{1},...,v_{2^{n}}$$, then we can take any initial probability
distribution $$\Pi^{0}=\sum_{i}{\alpha}_{i}v_{i}$$ and easily determine
$$\Pi^{0}P=\sum_{i}{\alpha}_{i}v_{i}P=\sum_{i}{\alpha}_{i}{\lambda}_{i}v_{i}$$.
Then after a large number of time steps $$t$$, we will have
$$\Pi^{0}P^{t}=\sum_{i}{\alpha}_{i}v_{i}P^{t}=\sum_{i}{\alpha}_{i}{\left({\lambda}_{i}\right)}^{t}v_{i}$$.
In general, it is hard to say anything about the spectral properties of
an arbitrary matrix. We know $$P$$ is a row-stochastic matrix,
$$\sum_{j}P_{ij}=1$$. But we want to put further restrictions on $$P$$ to
analyze the eigenvectors.

We will analyze Markov chains which are strongly connected (for any 2
states $$(i,j)\in S$$, there exists a path from $$i$$ to $$j$$). This is a
natural assumption since no matter what state we start in, we want to be
able to reach the target distribution in the limit.

The key assumptions we will make are that the Markov chain is
irreducible (all states communicate with each other: for all $$i,j\in S$$,
$$p_{ij}^{(n)}>0$$ for some $$n$$, meaning you can go from any state to any
other state for a large enough $$n$$) and the Markov chain is aperiodic
(there exists $$n$$ such that for all $$n'\geq n$$,
$$P(x_{n'}=i\mid x_{0}=i)>0$$).

$$\Pi^{*}$$ is a stationary probability distribution if
$$\Pi^{*}=\Pi^{*}P$$. In general, stationary probability distributions are
not unique. The irreducibility condition guarantees that the stationary
distribution is unique. For an example of what happens when we do not
have irreducibility, consider the case of two nodes with self-loops with
$$P=\begin{bmatrix}1 & 0\\
0 & 1
\end{bmatrix}$$.

$$\Pi^{*}$$ is the limiting distribution if for every initial probability
distribution $$\Pi^{0}$$, $$\lim_{n\to\infty}\Pi^{n}=\Pi^{*}$$. The
aperiodicity condition is necessary for the limit to exist. (Comment:
This is a technical condition. In practice, we might not need
aperiodicity.) For an example of what happens when we do not have
aperiodicity, consider $$P=\begin{bmatrix}0 & 1\\
1 & 0
\end{bmatrix}$$.

# Markov chains - proof of convergence

We will prove that if the Markov chain is irreducible and aperiodic,
then there exists a stationary distribution, the stationary distribution
is unique, and the Markov chain will converge to the stationary
distribution (note the Perron-Frobenius theorem).

If the Markov chain is irreducible and aperiodic, then the Markov chain
is primitive ($$\exists k$$ such that $$[P^{k}]_{ij}>0$$). $$[P^{k}]_{ij}$$
tells us the probability of going from state $$i$$ to state $$j$$ in exactly
$$k$$ steps. To see this, note that if the Markov chain is irreducible, it
means we can go from any node to any other node in a finite number of
steps (possibly depending on $$i,j$$). We want $$k$$ independent of $$i,j$$.
To do this, we can add self-loops which are guaranteed to exist by the
aperiodicity condition.

Without loss of generality, let us consider the case where $$[P]_{ij}>0$$.
We will prove that $$P^{n}\to W$$ as $$n\to\infty$$ for a $$W$$ where all of
the rows of $$W$$ are identical.

The intuition is that the stochastic matrix is doing averaging. The
biggest and smallest elements of the vector get closer to each other
because of the weighted average (contraction). Eventually, the vector
will converge to a constant.

Let $$P$$ be a $$r\times r$$ transition probability matrix with no zero
entries. Let $$d>0$$ be the smallest entry of $$P$$.

Let $$y\in{\mathbb{R}}^{r}$$ be a vector with largest component $$M_{0}$$
and smallest component $$m_{0}$$. Similarly, define $$M_{1}$$ to be the
largest component and $$m_{1}$$ to be the smallest component of
$$Py\in{\mathbb{R}}^{r}$$.

We will show that $$(M_{1}-m_{1})\leq(1-2d)(M_{0}-m_{0})$$. To prove this
bound, let us consider a “worst-case” vector which will give us the
tightest bound. Consider a vector $$y$$ which is $$M_{0}$$ everywhere except
$$m_{0}$$ at the spot corresponding to the entry with $$d$$ in $$P$$. Thus, we
see that the biggest $$M_{1}$$ can be is $$M_{1}\leq dm_{0}+(1-d)M_{0}$$.
Similarly, the smallest $$m_{1}$$ can be is $$m_{1}\geq dM_{0}+(1-d)m_{0}$$.
Subtracting the two inequalities gives the desired bound.

Let $$y\in{\mathbb{R}}^{r}$$ be an arbitrary vector. Let us study the
sequence $$P^{n}y$$.

\$$
\begin{eqnarray*}
 & M_{0}\geq M_{1}\geq M_{2}\geq...\\
 & m_{0}\leq m_{1}\leq m_{2}\leq...\\
 & m_{0}\leq m_{n}\leq M_{n}\leq M_{0}
\end{eqnarray*}
\$$
{: style="text-align: center"}

Since the sequences are bounded and monotonic, $$M_{n}\to M$$ and
$$m_{n}\to m$$ must converge.

We also know that 

\$$
\begin{eqnarray*}
M_{n}-m_{n} & \leq & (1-2d)(M_{n-1}-m_{n-1})\\
 & \leq & (1-2d)^{n}(M_{0}-m_{0})
\end{eqnarray*}
\$$
{: style="text-align: center"}

If $$r\geq2$$, then $$d\leq\frac{1}{2}$$ and $$0\leq1-2d<1$$, so as
$$n\to\infty$$, $$M_{n}-m_{n}\to0$$ and so we have $$M=m$$. Therefore,
$$\lim_{n\to\infty}P^{n}y=\mu$$, a vector $$\mu$$ where all of the entries
in $$\mu$$ are the same.

In particular, we can let $$y=e_{j}$$. $$P^{n}y\to w_{j}$$, a vector $$w_{j}$$
where all of the entries in $$w_{j}$$ are the same. This concludes the
proof that $$P^{n}\to W$$ as $$n\to\infty$$ for a $$W$$ where all of the rows
of $$W$$ are identical. The interpretation of this limit is that
regardless of the initial state we start in, the limiting probability
distribution is the same. If $$\Pi^{0}$$ is any probability distribution,
then $$\lim_{n\to\infty}\Pi^{0}P^{n}=\Pi^{0}W=w$$ where $$w$$ is any row of
$$W$$.

We will now show that $$w$$ is a stationary distribution of $$P$$. Let
$$W=\lim_{n\to\infty}P^{n}$$. Since $$P^{n+1}=P^{n}P$$, by taking limits on
both sides, we get $$W=WP$$. Row-wise, $$w=wP$$. We also see that $$w$$ is a
left eigenvector of $$P$$ with an eigenvalue of 1.
