---
layout: post
title: Generalizing satisfiability problems
---

In propositional logic, we used hard constraints to specify our system.
Today, we will consider several generalizations of propositional logic
and the Boolean satisfiability problem that allow us to formulate more
complex problems. When considering the problems that can be encoded in a
given framework, we will see we have the following spectrum of
representational power, from least to greatest: SAT $$\rightarrow$$
Weighted MAX-SAT $$\rightarrow$$ Markov logic networks $$\to$$ Factor
graphs.

# Minimum Vertex Cover (MAX-SAT)

Given an undirected graph $$G=(V,E)$$, we want to find a subset of
vertices $$U\subseteq V$$ such that for each $$(i,j)\in E$$, either $$i\in U$$
or $$j\in U$$ (every edge is incident to one of the vertices in $$U$$). The
minimum vertex cover problem is finding a smallest vertex cover. A
trivial vertex cover would be the set of all vertices. A real-life
example is a graph where the edges represent roads and finding a vertex
cover could represent installing a small number of cameras at the
intersections which cover all of the roads.

Consider formulating this problem the following way. We introduce one
Boolean variable for each vertex, such that $$X_{i}=1$$ iff $$i\in U$$. Then
for each $$(i,j)\in E$$, we introduce the clause
$${\left(X_{i}\lor X_{j}\right)}$$. We want to minimize $$\sum_{i}X_{i}$$.
The hard requirement is finding a valid vertex cover while the soft
requirement is minimizing the size of the vertex cover.

An extension of SAT is Weighted MAX-SAT where we add weights to each
clause. For any assignment $${\sigma}\in\{0,1\}^{n}$$, define
$$score({\sigma})$$ to be the sum of the weights of the clauses that
$${\sigma}$$ satisfies and $$cost({\sigma})$$ to be the sum of the weights
of the clauses that $${\sigma}$$ does not satisfy. Then the optimization
problem is finding the assignment $${\sigma}$$ with maximum score or
minimum cost.

To formulate the minimum vertex cover problem as a MAX-SAT problem, we
find the assignment $${\sigma}$$ that minimizes the cost of the following
optimization problem: for each $$(i,j)\in E$$,
$${\left(X_{i}\lor X_{j}\right)}$$ with weight $$\infty$$ and for each
$$i\in V$$, $$\neg X_{i}$$ with weight 1.

To solve MAX-SAT, we can adapt solvers for SAT. Local search algorithms
and greedy heuristics can use the cost or score function when deciding
between possible moves. During systematic search, when exploring the
search tree, solvers can use branch and bound techniques when deciding
which subtrees to prune. This involves keeping track of lower bounds or
upper bounds based on the best solution found so far and guesses for
bounds in the current subtree for either the cost or score function. To
get the bounds for the subtrees, one technique is relaxing the domain of
an integer optimization problem from $$x\in\{0,1\}$$ to $$x\in[0,1]$$ and
solving the easier optimization problem.

# Markov logic networks

Propositional logic fails when some statements in the knowledge base are
inconsistent. Instead of having logical statements which are either true
or false in a world, we want to generalize to statements which hold with
some weight (probability). Markov logic networks are such a
generalization, combining logic and probability.

The idea is to encode our problem using soft constraints among the
variables in our model. By specifying weights on the soft constraints,
we will then have a compact way to represent the most likely worlds
consistent with our knowledge.

For any configuration (state), defined by an assignment of values to the
variables in our model, we can compute the weight of that configuration.
Formally, we define a cost function, mapping each configuration
$$x\in\{0,1\}^{n}\to\text{cost}(x)$$, and we let the weight of a
configuration be equal to $$w(x)=\exp{\left(-\text{cost}(x)\right)}$$.

Define
$$p(x)=\frac{w(x)}{Z}=\frac{1}{Z}\exp{\left(-\text{cost}(x)\right)}=\frac{1}{Z}\exp{\left(-\sum_{\text{clauses }c}w_{c}1_{c}(x)\right)}$$
where $$1_{c}(x)$$ is an indicator function which is 1 if clause $$c$$ is
violated. By defining the normalization constant
$$Z=\sum_{x\in\{0,1\}^{n}}\exp{\left(-\text{cost}(x)\right)}$$, we ensure
we have valid probabilities satisfying $$0\leq p(x)\leq1$$ and
$$\sum_{x\in\{0,1\}^{n}}p(x)=1$$.

We see that the probability of an assignment factorizes over the
clauses: 

\$$
\begin{eqnarray*}
p(x) & = & \frac{1}{Z}\exp{\left(-\sum_{c}w_{c}1_{c}(x)\right)}\\
 & = & \frac{1}{Z}\prod_{c}\phi_{c}(x_{c})
\end{eqnarray*}
\$$
{: style="text-align: center"}

for an appropriately defined $$\phi_{c}(x_{c})$$, one factor for each
clause $$c$$.

In Markov logic networks, a basic optimization problem is finding the
configuration $$X$$ satisfying $${\mathrm{argmax}}_{X}P(X\mid E)$$ for some
evidence $$E$$. Note that
$${\mathrm{argmax}}_{x}\log p(x\mid y)={\mathrm{argmax}}_{x}\big[-\log(Z)-\text{cost}(x,y)\big]$$
is equivalent to $${\mathrm{argmin}}_{x}\text{cost}(x,y)$$.

# Factor graphs

An arbitrary probability distribution over $$n$$ binary variables requires
$$2^{n}-1$$ parameters to represent. Factor graphs allow us to study
probability distributions which factorize and can be represented in a
compact way.

The basic setup for factor graphs is for each $$x\in\{0,1\}^{n}$$, let
$$p(x)=\frac{1}{Z}\prod_{c}\phi_{c}(x_{c})$$ over all cliques $$c$$ where
each $$\phi_{c}(x_{c})$$ is a function which maps
$$\{0,1\}^{|c|}\to{\mathbb{R}}^{+}$$ .

We can encode satisfiability problems into the factor graph
representation as follows. Given a CNF formula, we can represent each
clause by a clique which is a function of the variables in that clause.
If the clause is satisfied by some partial assignment $$x_{c}$$, we let
$$\phi_{c}(x_{c})=1$$, and otherwise we let $$\phi_{c}(x_{c})=0$$. Then an
assignment $$x$$ that satisfies all of the clauses will have
$$\prod_{c}\phi_{c}(x_{c})=1$$ and otherwise will have
$$\prod_{c}\phi_{c}(x_{c})=0$$.

In factor graphs, some probabilistic reasoning tasks that we might want
to perform are as follows:
1.  Marginal and conditional probabilities:
    $$p(x_{A}=\overline{x_{A}}\mid x_{B}=\overline{x_{B}})$$
2.  Sampling: $$x\sim p(x)$$
3.  Compute the partition function $$Z=\sum_{x}\prod_{c}\phi_{c}(x)$$ (the
    number of satisfying assignments in SAT problems)
4.  Most likely state of the world given evidence: $$\max_{x}p(x\mid y)$$

**Theorem**: Problems 1 through 3 are reducible to one another.

***Proof*** ($$3\Rightarrow1$$): If we can compute partition functions,
then we can compute
$$P(x_{1}=T)=\frac{\sum_{x\in SAT}{\mathbb{I}}[x_{1}=T]}{Z}$$ by first
computing the partition function $$Z$$ of the original graphical model,
clamping the variable $$x_{1}=T$$, and then counting the number of
satisfying assignments with $$x_{1}=T$$. The marginal probability is the
ratio of the total weight in the subtree rooted at $$x_{1}=T$$ and the
total weight of the tree.

***Proof*** ($$1\Rightarrow3$$): If we can compute all marginal
probabilities, then we can compute the partition function $$Z$$ as
follows. Rewrite
$$Z=\frac{Z}{Z(x_{1}=T)}\frac{Z(x_{1}=T)}{Z(x_{1}=T,x_{2}=T)}...Z(x_{1}=T,...,x_{n}=F)$$
where we choose the assignments $$x_{1},...,x_{n}$$ so that the partition
function counts are non-zero and the ratios above are inverse ratios of
marginal probabilities.

***Proof*** ($$2\Rightarrow1$$): If we can draw samples
$$X_{1},...,X_{T}\sim p(x)$$, then we can compute the marginal probability
$$P(x_{1}=\text{True})$$ as the empirical frequencies
$$\hat{g}(X_{1},...,X_{T})=\frac{1}{T}\sum_{t=1}^{T}{\mathbb{I}}[X_{t}[1]=\text{True}]$$
for $$T$$ sufficiently large.

***Proof*** ($$1\Rightarrow2$$): If we can evaluate marginal
probabilities, then we can sample. Start at the root of the tree. Say
this is variable $$x_{1}$$. We compute the marginal probability that
$$x_{1}$$ is true or false. We draw a random number
$$r\sim\text{Uniform(0,1)}$$ and go down the branch $$x_{1}=T$$ if
$$r<P(x_{1}=T)$$ and $$x_{1}=F$$ otherwise. This works because of the chain
rule since
$$P(x_{1},x_{2},...,x_{n})=P(x_{1})P(x_{2}\mid x_{1})P(x_{3}\mid x_{1},x_{2})...$$
