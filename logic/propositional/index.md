---
layout: post
title: Propositional Logic
---
We begin our course by studying propositional logic. After we introduce
the definitions, we will discuss satisfiability in propositional logic
and then move on to state of the art techniques to solve systems specified
with propositional logic.


## Definitions

Propositional logic is a formal language used to specify knowledge
in a mathematically rigorous way. We first define the syntax (grammar)
and then the semantics (meaning) of sentences in propositional logic.


### Syntax

We denote our propositional variables by $$p_{1},...,p_{n}$$ where
each $$p_{i}$$ is a binary variable that can be true or false.

Sentences are defined recursively as follows: 
- A propositional variable is a sentence (this is known as an atomic sentence)
- If $$\alpha$$ and $$\beta$$ are sentences, then $$\neg\alpha$$, $$\alpha\lor\beta$$, and $$\alpha\land\beta$$ are valid sentences


### Semantics

We have to define the notion of a world. A world $$\omega$$ is an assignment
of a truth value to each propositional variable. Note that there are
$$2^{n}$$ possible worlds if we have $$n$$ propositional variables.

We determine if a sentence $$\alpha$$ is true in a world $$\omega$$
using the following recursive rules:
- $$\omega\models p_{i}$$ if and only if $$\omega(p_{i})$$ is true
- $$\omega\models\neg\alpha$$ if and only if $$\omega\not\models\alpha$$
- $$\omega\models\alpha\lor\beta$$ if and only if ($$\omega\models\alpha$$)
  OR ($$\omega\models\beta$$)
- $$\omega\models\alpha\land\beta$$ if and only if ($$\omega\models\alpha$$)
  AND ($$\omega\models\beta$$)
- (Note that $$\alpha\Rightarrow\beta$$ is syntactic sugar for $$\neg\alpha\lor\beta$$, and we can use the rules above)


### Knowledge Base

A knowledge base is a collection of sentences $$\alpha_{1},\alpha_{2},...,\alpha_{k}$$
that we know are true, i.e. $$\alpha_{1}\land\alpha_{2}\land...\land\alpha_{k}$$.
The sentences in the knowledge base allows us to compactly specify
the allowable states of the world. By adding new sentences, the number
of possible worlds consistent with our knowledge base could remain
the same (if the new sentence is entailed by existing sentences),
decrease (if we gain new information), or go to zero (if the new sentence
is inconsistent with the existing knowledge base).

We will make use of the following definitions:
- A sentence is satisfiable (consistent) if there exists at least one
world that makes the sentence true, i.e. $$Models(\alpha)\ne\phi$$.
- A sentence is valid if the sentence is true in every possible world,
i.e. $$Models(\alpha)=\Omega=\{0,1\}^{n}$$.
- Two sentences are equivalent if they are true in the same models,
e.g. $$\alpha\lor\beta$$ and $$\beta\lor\alpha$$.


## Satisfiability


### Inference reduces to satisfiability

The basic inference problem in the propositional logic system is the
following: given the knowledge that we have about the world, is it
the case that some property must always hold? In other words, what
conclusion can we reach about some other sentence? The three possible
cases that could result are always true, depends, and always false.

Inference problem: Does $$\alpha\Rightarrow\beta$$? We can solve this
by checking if $$\alpha\land\neg\beta$$ is satisfiable.
1. If not satisfiable, then we know that $$\alpha\Rightarrow\beta$$. This
   is similar to the idea behind proof by contradiction.
2. If satisfiable, then there are two possible cases. Check if $$\alpha\land\beta$$
   is satisfiable. If $$\alpha\land\beta$$ is satisfiable, then there
   is no definitive answer. If $$\alpha\land\beta$$ is not satisfiable,
   then we know that $$\alpha\Rightarrow\neg\beta$$.

Many problems across different domains can be encoded using propositional
logic. Inference in this space basically boils down to checking satisfiability.
For examples, problems such as solving Sudoku puzzles, hardware verification,
and planning can be encoded as satisfiability problems and then solved
efficiently using state of the art satisfiability solvers. Since a
lot of research has gone into improving general SAT solvers, if some
domain-specific problem has an efficient transformation into an instance
of satisfiability checking, then SAT solvers can often solve these
domain-specific problems efficiently.


### Conjunctive normal form

To check the satisfiability of a sentence, the first step is to convert
sentences into normal form which is easier for satisfiability solvers
to handle. A sentence is in conjunctive normal form (also known as
clausal norm form or CNF) if a sentence is a conjunction of disjunctions.

Given any formula, we can always convert it into CNF using the following
procedure:
1. The first step is to remove syntactic sugar, e.g. change $$\alpha\Rightarrow\beta$$
   into $$\neg\alpha\lor\beta$$. After this step, we should only have
   negations, conjunctions, and disjunctions.
2. Push negations in. Negations should only appear in front of propositional
   variables. For example, change $$\neg\neg\alpha$$ into $$\alpha$$ and
   $$\neg(\alpha\lor\beta)$$ into $$(\neg\alpha\land\neg\beta)$$.
3. Distribute disjunctions over conjunctions, e.g. change $$\alpha\lor(\beta\land\gamma)$$
   into $$(\alpha\lor\beta)\land(\alpha\lor\gamma)$$. (Note that converting
   a sentence which is composed of many disjunctions of conjunctions
   into CNF using this procedure can create an exponential number of
   clauses.)

Checking satisfiability in CNF form is easy. A clause is satisfiable
if and only if at least one of the literals (a variable or the negation
of a variable) is set to true. A sentence is satisfiable if and only
if each clause is satisfiable.

Suppose we want to test the satisfiability of a formula $$\Delta$$
in CNF. Consider what happens when we set a propositional variable
$$P$$ to true. If P does not belong to a given clause, then that clause
is unaffected. If P belongs to a given clause, then that clause becomes
satisfied and we can remove that clause from the formula. The resulting
formula can be expressed as $$\Delta\vert P=\{\alpha\backslash\{\neg P\}\mid\alpha\in\Delta,P\notin\alpha\}$$.


## Satisfiability solvers


### Brute force

The brute force approach to checking satisfiability is to go through
all possible worlds and check if the formula is satisfied. To prove
unsatisfiability, we would need to try all possible worlds. In the
worst case, checking satisfiability is NP-complete because the number
of possible worlds is exponential in the number of variables, and
any kind of reasoning algorithm would take exponential time. Nonetheless,
in practice, one can usually solve many kinds of real-world problems
tractably using the techniques discussed below.


### Early stopping

One improvement over brute force search is to do early stopping. We
can visualize the search process as traversing a binary tree where
each node corresponds to a variable and the left branch corresponds
to setting that variable to true and the right branch corresponds
to setting that variable to false. After making assignments to a couple
of variables, we might be able to declare early success if we find
a satisfying assignment. In addition, whenever we encounter a formula
that is already unsatisfiable, we backtrack and avoid the need to
search the subtree rooted at that node.

Note that this algorithm requires us to choose a variable ordering.
Each time we go down a branch and make a variable assignment, we can
simplify the CNF. At every step, we check if there exists an empty
clause which means that the formula cannot be satisfied. If we eliminate
all of the clauses, then the formula is satisfiable.


### Unit resolution

Whenever there is a clause with only one literal, then the only way
to satisfy the formula is to set the corresponding variable so that
clause is true. For example, if $$\Delta$$ consists of $$\{B\},\{\neg B\lor\neg C\},\{C\lor\neg D\}$$,
then we must set B to true, then set C to false, and finally D to
false.

Another improvement is if a variable only appears in the formula in
its negated or unnegated form, then we know how to set the value of
that variable.


### DPLL algorithm

The DPLL algorithm combines DFS with early stopping, unit propagation
and pure literals. With these heuristics, DPLL is a very effective
backtracking SAT solver. The recursive algorithm for DPLL is as follows.

$$DPLL(\phi,\alpha)$$:
1. If $$\phi\vert\alpha$$ is empty, return satisfiable.
2. If $$\phi\vert\alpha$$ contains an empty clause, return unsatisfiable.
3. If $$\phi\vert\alpha$$ contains a unit clause $$\{p\}$$, return $$DPLL(\phi,\alpha p)$$.
4. If $$\phi\vert\alpha$$ has a pure literal $$p$$, return $$DPLL(\phi,\alpha p)$$.
5. Let $$p$$ be a literal from a minimum size clause of $$\phi\vert\alpha$$.
If $$DPLL(\phi,\alpha p)$$ returns satisfiable, return satisfiable.
Else, return $$DPLL(\phi,\alpha\neg p)$$.


### Clause Learning

Fundamentally, the biggest limitation from a backtracking solver like
DPLL is that you don't learn from your mistakes so you might make
the same mistakes over and over. For example, suppose that the variable
ordering we choose for DPLL is unlucky in that the decision we make
for the first variable leads the rest of the clauses to be inconsistent
but unit propagation cannot detect this so we end up visiting each
assignment to the variables that is not a satisfying assignment.

Clause learning adds clauses that are implied by the knowledge base,
which are discovered during the search process, to the knowledge base.
Adding clauses that are implied by the knowledge base, particularly
short clauses, can empower unit propagation and allow a different
way of searching the tree that is non-chronological.

We will use a graph-based data structure, called an implication graph,
that has nodes corresponding to variable assignments (either decisions
we make when branching or implications via unit propagation). The
directed edges in the graph will record dependencies among the variable
assignments. Each node in the implication graph has the form $$l/V=v$$
which means that at level $$l$$, variable $$V$$ has been set to value
$$v$$. Whenever we branch on a variable, we create a node for this
variable assignment but do not add any edges. Whenever unit propagation
allows us to derive the value of a variable, we create a node for
this variable assignment, and we add directed edges to this new node
from the nodes corresponding to the variable settings that allowed
unit propagation to make this derivation and label each edge with
the clause that was used to make the derivation.

When we reach a contradiction, we can build a conflict graph and identify
opportunities to learn from mistakes. In an implication graph which
terminates in a contradiction, every cut that separates the decision
variables from the contradiction defines a conflict set. The nodes
in the conflict set are exactly the source nodes of all of the (directed)
edges that cross the cut.

Though there may be several conflict sets, a typical approach is to
identify a cut so that there is only one decision variable at the
highest (last) decision level on the reason side and then backtrack
to the second highest (second to last) decision level. Following this
backtracking procedure, the clause that was added is guaranteed to
trigger unit propagation.

Another approach is to identify a Unique Implication Point (UIP) which
is a node at the highest decision level that appears in every path
from the highest decision level to the contradiction. If there is
more than one UIP, often we choose a UIP that is closest to the contradiction
since this might allow shorter clauses.

Non-chronological backtracking is sound and complete assuming we keep
all of the clauses that we learned. Note that non-chronological backtracking
might go down the same path multiple times.


### Engineering considerations

An efficient implementation of unit propagation is very important
since a lot of time is spent doing unit propagation. We can implement
unit propagation more efficiently by using a lazy approach. We only
watch two variables in each clause. Every time we assign a variable,
we only need to check the clauses for which that variable is watched.
Whenever a watched variable becomes assigned to true or false, we
check each clause for which that variable is watched: if we can we
do unit propagation, we do unit propagation and that clause becomes
satisfied; otherwise, we find another variable in that clause to watch.

In what order do we assign the variables and values? We try to keep
track of how important each variable is. One heuristic is that
if a variable appears in many clauses, it's probably more important.

Restarts with a different random seed (while keeping learned clauses)
can be very helpful in practice because the distribution of time SAT
solvers take to terminate has a heavy tail. The intuition is that
a SAT solver can get lost in a large part of the state space where
there is no solution. If you keep doing restarts, how can you guarantee
completeness? One heuristic is to double the restart cutoff each time.