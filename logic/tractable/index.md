---
layout: post
title: Tractable classes of satisfiability problems
---

We now discuss two special cases of satisfiability problems that can be
solved in polynomial time, Horn SAT and 2-SAT. These problems define
subclasses of general CNF formulas which satisfy some specific
structure. By restricting the expressive power of the language, these
subclasses of problems become easier to solve. The algorithms for
solving these problems are essentially based on unit propagation.

## Horn SAT

We begin with the relevant definitions for a Horn formula:
-   A literal is a positive literal if it is some variable. A literal is
    a negative literal if it is the negation of some variable.
-   A clause is positive if all of the literals are positive. A clause
    is negative if all of the literals are negative.
-   A clause is Horn if it has at most one literal that is positive. For
    example, the implication $$(p\land q)\Rightarrow z$$ is equivalent to
    $$\neg p\lor\neg q\lor z$$ which is horn.
-   A formula is Horn if it is a formula formed by the conjunction of
    Horn clauses. For example, the formula
    $${\left(\neg p\lor\neg q\lor z\right)}\land{\left(q\lor\neg z\right)}$$
    is Horn while
    $${\left(\neg p\lor q\lor z\right)}\land{\left(q\lor\neg z\right)}$$
    is not Horn.

**Lemma**: Let $$S$$ be a set of unsatisfiable clauses. Then $$S$$ contains
at least one positive clause and one negative clause.

***Proof***: Suppose that the formula contains no positive clause. Then
every clause contains at least one negative literal. We can satisfy the
formula with a truth assignment that assigns false to all of the
variables.

**Theorem**: Let $$S$$ be a set of Horn clauses. Let $$S'$$ be the set of
clauses obtained by running unit propagation on $$S$$. Then $$S'$$ is
satisfiable if and only if the empty clause does not belong to $$S'$$.

***Proof*** ($$\Rightarrow$$): If the empty clause belongs to $$S'$$, then
$$S'$$ cannot be satisfiable because unit propagation is sound.

***Proof*** ($$\Leftarrow$$): If $$S$$ is horn, then $$S'$$ is also horn.
Assume $$S'$$ is unsatisfiable. By Lemma 1, $$S'$$ contains at least 1
positive clause and 1 negative clause. A clause that is both positive
and horn must either be a unit clause or an empty clause. We cannot
derive a unit clause since we’ve run unit propagation until a fixed
point, so there must exist an empty clause.

## 2-SAT

A formula is in k-CNF if it is in CNF and each clause has at most k
literals. For example, the formula
$${\left(p\lor q\right)}\land{\left(r\lor s\right)}$$ is in 2-CNF while
$${\left(p\lor q\right)}\land{\left(r\lor s\lor t\right)}$$ is not in
2-CNF. The following algorithm can be used to determine the
satisfiability of a 2-CNF formula in polynomial time.
1.  \$${\Gamma}\leftarrow KB$$
2.  while $${\Gamma}$$ is not empty do:
    1.  \$$L\leftarrow\text{pick a literal from }{\Gamma}$$
    2.  \$${\Delta}\leftarrow\text{UP}({\Gamma},L)$$
    3.  if $$\{\}\in{\Delta}$$ then
        1.  \$${\Delta}\leftarrow\text{UP}({\Gamma},\neg L)$$
        2.  if $$\{\}\in{\Delta}$$ then return unsatisfiable
    4.  \$${\Gamma}\leftarrow{\Delta}$$
3.  Return satisfiable

**Lemma**: If $${\Gamma}$$ is a 2-CNF formula in which the literal $$L$$
occurs, then either:
1.  UP$$(\Gamma,L)$$ contains the empty clause $$\{\}$$, so
    $${\Gamma}\models\neg L$$.
2.  UP$$(\Gamma,L)$$ is a proper subset of $${\Gamma}$$.

***Proof***: For each clause, we consider one of the three cases.
1.  If the clause contains $$L$$, then the clause is satisfied.
2.  If the clause contains $$\neg L$$, then this clause becomes a unit
    clause and unit propagation is triggered.
3.  Otherwise, the clause remains unchanged.

***Proof of correctness*** ($$\Rightarrow$$): If the algorithm returns
satisfiable, the formula is satisfiable since the algorithm relies on
unit propagation and branching.

***Proof of correctness*** ($$\Leftarrow$$): Consider the formula
$${\Gamma}$$ at the beginning of the iteration in which we return
unsatisfiable. Since $${\Gamma}$$ is a 2-CNF formula,
$${\Gamma}\subseteq KB$$ (meaning the set of clauses in $${\Gamma}$$ is
always a subset of the clauses in $$KB$$ ), so if $${\Gamma}$$ is
unsatisfiable, then $$KB$$ is unsatisfiable. We know that both
$${\Gamma}\land L$$ and $${\Gamma}\land\neg L$$ are unsatisfiable. This
implies that $${\Gamma}$$ is unsatisfiable, so $$KB$$ is unsatisfiable.
