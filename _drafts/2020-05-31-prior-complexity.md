---
layout: post
title: Prior complexity for generalisation
---

I want to know; how does the generalisation error of my classifier scale as a function of the information in my prior?


## Background

Generalisation is only possible when there is some relationship between the models generating training and testing data.

In the simplest case the relationship is the identity, thus training and testing are the same.

Next is where they have the same model, but are sampled under different distributions.

<!--
Also.
- Diff model + same dist.
- Diff model + diff dist.
(do we ever care about these cases?!)
-->

Have some prior over the relationship between  $P(\theta_{test},\theta_{train})$.

<!--
When can we expect generalisation!?
Given problem X, priors P, data D.
What is the test accuracy likely to be?
-->

#### NNs

A NN + SGD has many build in priors.
Consider the function space realisable by a neural network. Training via SGD prefers some functions over others, despite the fact that, on the observed data they perform the same.

<!--
More correctly?? it prefer some weight settings over others, which yields a preference over functions.
-->

Thus SGD gives a prior over functions $P(f)$. And we want to know $P(f| D)$.

## Motivation

Sample complexity captures the data-dependent generalisation (somewhat akin to interpolation). For example...

Now, consider generalisation between two different training distributions $P, Q$ where the supports do not intersect. More data from $P$ does not help us learn anything about $Q$. So a sample complexity analysis will not help.

Rather, to understand the effects of our models and algorithms (eg NNs + SGD) on generalisation we need to understand how their priors (or inductive biases, whether implicit or explicit) effect sample complexity.

<!--
for starters, let's just consider how generalisation accuracy scales with prior info.

ultimate goal, how does generalisation accuracy with prior info and more data.
given these priors, what is the sample complexit?
give other priors, what is the sample complexity?
-->

## Formal setting

Sample a dataset $D_{train} \sim P(x, y | \varphi)P(\varphi)$ from some training distribution.

Evaluate on some testing distribution $P_{test}(x, y)$.

$$
\mathcal L(\theta_t) = \mathop{\mathbb E}_{x, y \sim P_{test}}[\ell(f(x, \theta_t), y)]
$$

Find the optimal parameters given some prior $P(\theta)$

$$
\begin{align*}
P(\theta | D_{train}) = \frac{P(D_{train} | \theta) P(\theta)}{P(D)} \\
\end{align*}
$$


## Example

???

## Quantifying a prior

Let $i = \text{MI}(P(\theta) ; P_{test}(x, y))$.
As $i$ increases, how does




## Notes

- I am expecting non-linear ('phase transitions') when there is enuogh information to convey some prior which them makes the problem scale better with the data. ??? really. would incrementally more info not necessarily help?
