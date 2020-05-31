---
layout: post
title: Automated science
---

Automated exploration of a world.
Want to build an accurate model as fast as possible.

$$
\text{min} \mathop{\mathbb E}_{M\sim \mathcal M}[\text{Regret}(\text{alg}, M)]\\
\text{Regret}() = \sum_t \int_\pi \text{KL}(P_{\theta_t}(\tau| \pi, D_t); P(\tau| \pi)) \\
$$

## Heuristics for efficiently building a model

- Pick actions / experiments that uncertainty.
- Measure everything (only relevant in POMDPs?!)
- Reductionism. (modularise the model, then indpendently test.)
- Falsification.
- Find the simplest possible test.
- Incorporate (physical) priors / Occam's razor

#### Min uncertainty

$$
\mathop{\text{max}}_{\pi} H(P(\tau | \pi, D)) \\
P_{\theta}(\tau | \pi, D) = P(s_0 | D)\prod_{t=0}^T P(s_{t+1} | s_t, a_t, D) \pi(a_t | s_t)
$$

Is taking actions that minimise the entropy of the posterior the dominant strategy for inferring a model?

Sampling highly variant trajectoies many times to reduce their estimated variance may not be a good use of samples. Rather, it might be smarter (better at reducing uncertainty) to know (with high certrainty) the mechanisms by which trajectories are generated ($P(s'|s, a)$).

How do we formulate this?
Is it always true?


#### Define reductionism

> the practice of analysing and describing a complex phenomenon in terms of its simple or fundamental constituents, especially when this is said to provide a sufficient explanation.



Given a disentangled representation. Test

So the decomposition of the trajectoies into;

$$
P_{\theta}(\tau | \pi, D) = P(s_0 | D)\prod_{t=0}^T P(s_{t+1} | s_t, a_t, D) \pi(a_t | s_t)
$$

is a type of reductionism.

## Physical priors

Occam's razor.
We believe that certain models, $P(s' | s, a)$ are more likely that others.
(this is the same as having a prior over trajectories? $P(\tau | \pi)$ )?

$$
P(\tau| \pi, D)P(\pi, D) = P(D, \pi | \tau )P(\tau)
$$
