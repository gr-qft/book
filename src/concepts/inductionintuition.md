# Thinking About Recursion Inductively

There is a strong association between mathematical induction and recursion, especially in SML. Often times, we'll be able to use similar vocabularies when describing SML problems and mathematical induction. In particular, we're going to be use the words **base case, induction hypothesis, and induction step** to describe both types of problems.

## Inductive Intuition

Approaching induction proofs can fall along the following line of logic:

1. Solve for your **base cases**.
2. Define your **inductive hypothesis**.
3. Assume the correctness of your **inductive hypothesis** to show the correctness of your **inductive step**.

We can similarly apply this line of logic to solving problems with SML functions! Let's take a look at a common recursive problem.

## Tree Sums

### An Exploration

```sml
datatype int tree = Empty | Node of int tree * int * int

# Define a function that returns the sum of all the integers in a tree
# REQUIRES: true
# ENSURES:  treeSum(T) ==> the sum of the integers at each node of T.
fun treeSum (T : int tree) : int = ...
```

Note that in SML, the `tree` datatype is recursively defined. This is a good hint that we should be using recursive/inductive strategies to approach this problem. Suppose you're asked to prove the following theorem:

**Theorem:** For all `T : int tree`, `fun treeSum(T)` is correct.

Let's not worry about formalizing this proof too much so that we can focus on the **inductive intuition** of it. If we were to prove this using induction, we'll need a **(1) base case, (2) induction hypothesis, and (3) induction step.**

#### 1. Solving for your base cases

Let's first think about proving the base case: `T = Empty`. What does it mean for `treeSum(Empty)` to be correct? Well, an `Empty` tree does not have any nodes, and if there are no nodes, there are no `int values`. The sum of nothing is 0. That wasn't so bad! Let's write that in a proof-like manner:

> **Base Case:** `T = Empty`

> - `treeSum(Empty) ==> 0` because an `Empty` int tree does not have an int value.

That wasn't so bad. If we have an empty node, we can't have a value there, and so the sum is 0. Before we move on to solving the recursive step, let's tie in this idea of how recursion and induction are related. In our proof, we say that `treeSum(Empty)` is correct when it evaluates to 0. Let's use this as an answer to how to define the base case of our function:

```sml
fun treeSum(Empty : int tree): int = 0
```

Nice job! We've leveraged inductive reasoning to help us define the base case for our recursive problem. Let's move on to something a little harder and may be less obvious than what we've done here.

#### 2. Define your inductive hypothesis

The next step in our proof is to define the inductive hypothesis. Here, we'll assume the correctness of a smaller part, then use that to prove the correctness of a bigger part. More specifically, we'll be using some ideas of [structural induction](https://smlhelp.github.io/#todolinktostructuralinductionsection) for this problem. Let's elaborate more on that:

> **Induction Hypothesis:** Assume for all `L : int tree` and `R : int tree` that `treeSum(L)` is correct and `treeSum(R)` is correct.

Because we've shown our base case to be true, let's assume the recursive structures (the left subtree `L` and the right subtree `R`) to be correct. Just like how in induction we can use these nuggets of information to help us prove our **inductive step**, we can do the same to help us solve the SML function.

#### 3. Assume the inductive hypothesis to show the inductive step.

What nuggets of information do we know from the previous step, and how can we use that to help me with inductive step? we know that both `treeSum(L)` and `treeSum(R)` are correct by the **inductive hypothesis (IH)**. If they are correct, then their outputs represent the sum of all the integers in them. For `val a = treeSum(L)`, `a` is the sum of all integers in the int tree `L`, and for `val b = treeSum(R)`, `b` is the sum of all integers in the int tree `R`.

We also know that since `L` and `R` are the left and right subtrees of `T`, by definition, they represent all nodes of `T` (except the root node). Then, to get sum of `T`, I just need the sum of `L`, `R`, and the value of the root node! Let's formalize this line of logic a bit more:

> **Inductive Step:** `T = Node(L,x,R)`

> - `treeSum(L)` is correct by **IH**
> - `treeSum(R)` is correct by **IH**
> - "All integers in `T`" are represented by "all integers in `L`", "all integers in `R`", and `x` by definition of trees.
> - The sum of "all integers in `T`" then is the sum of "all integers in `L`, "all integers in `R`", and `x`.
> - In other words, `treeSum(L) + treeSum(R) + x` by definition of `treeSum`.
> - `treeSum(L) + treeSum(R) + x` correctly represents the sum of all integers in `T` by math.
> - `treeSum(T)` is correct

Using the logic needed to complete the proof, we were able to arrive at how to implement our function! Let's translate the above logic into SML:

```sml
fun treeSum(Empty : int tree): int = 0
  | treeSum(Node(L,x,R)) = treeSum(L) + treeSum(R) + x
```

### Overview

We are able to see many similarities between our reasoning for the correctness proof and the implementation in SML.

#### Proof:

**Theorem:** For all `T : int tree`, `fun treeSum(T)` is correct.
**Base Case:** `T = Empty`

- `treeSum(Empty) ==> 0` because an `Empty` int tree does not have an int value.

**Induction Hypothesis:** Assume for all `L : int tree` and `R : int tree` that `treeSum(L)` is correct and `treeSum(R)` is correct.
**Inductive Step:** `T = Node(L,x,R)`

- `treeSum(L)` is correct by **IH**
- `treeSum(R)` is correct by **IH**
- "All integers in `T`" are represented by "all integers in `L`", "all integers in `R`", and `x` by definition of trees.
- The sum of "all integers in `T`" then is the sum of "all integers in `L`, "all integers in `R`", and `x`.
- In other words, `treeSum(L) + treeSum(R) + x` by definition of `treeSum`.
- `treeSum(L) + treeSum(R) + x` correctly represents the sum of all integers in `T` by math.
- `treeSum(T)` is correct

#### Function:

```sml
fun treeSum(Empty : int tree): int = 0
  | treeSum(Node(L,x,R)) = treeSum(L) + treeSum(R) + x
```

> The base case for `treeSum` is that an `Empty` tree has a sum of 0. Let's define the inductive hypothesis to be that for some tree `T`, that `treeSum` is correct for it's left subtree and right subtree. Define my inductive step to be for a tree `T = Node(L,x,R)`. By the definition of trees, I know that all integers in `T` are represented by the integers in `L`, `R`, and the integer `x`. If I sum all of these, I will get `treeSum(T)`. By assuming the **IH**, I can say that `treeSum(L)` and `treeSum(R)` are correct. Therefore, by math, I will say that `treeSum(T) = treeSum(L) + treeSum(R) + x` by the above reasoning.

## QED

And like that, we're able to leverage mathematical induction to help us find the solution to part of an SML function. You may be thinking that this may be obvious. For some people, it is! But for others, it may not be as clear to approach problems like this at first.