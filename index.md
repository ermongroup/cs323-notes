---
layout: post
title: Contents
---

These notes are based on Stanford [CS323](http://cs.stanford.edu/~ermon/cs323/index.html), taught by [Stefano Ermon](http://cs.stanford.edu/~ermon/), and have been written by Michael Zhu. The notes are still under construction! If you find any typos or can contribute to help make the notes better, please let us know, or submit a pull request with your fixes to our [Github repository](https://github.com/ermongroup/cs323-notes).

This course is a graduate level introduction to automated reasoning techniques and their applications, covering logical and probabilistic approaches. Topics include: logical and probabilistic foundations, backtracking strategies and algorithms behind modern SAT solvers, stochastic local search and Markov Chain Monte Carlo algorithms, classes of reasoning tasks and reductions, and applications.


## Logical Reasoning

- [Representation - propositional logic](logic/representation/): Definitions: syntax, semantics, knowledge base. Satisfiability: inference reduces to satisfiability, conjunctive normal form.
- [Inference - SAT solvers](logic/inference/): Brute force, early stopping, unit resolution, DPLL algorithm, conflict-driven clause learning, engineering considerations, tutorial on practical SAT solvers.
- [Tractable classes of SAT problems](logic/tractable/): Horn SAT and 2-SAT.
- [Inference - random walk SAT solvers](logic/random_walk/): Introduction, review of Markov chains. Random walk algorithm for 2-SAT and analysis. Random walk algorithm for 3-SAT and analysis. Other variants.
- [References](logic/references/)

## Probabilistic Reasoning

- [Generalizing satisfiability problems](probabilistic/generalizing/): Minimum Vertex Cover (Weighted MAX-SAT). Markov logic networks. Factor graphs.
- [Markov chains](probabilistic/markov_chains/): Markov Chain Monte Carlo (MCMC). Markov chains - introduction. Markov chains - proof of convergence.