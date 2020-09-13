---
layout: post
title: Inductive biases for efficient exploration
---

Inductive bias in exploration.

What priors could make sense in exploration?

Want to explore first;
- states that we can control,
- states that allow us to control many other dimensions of the state
- states that behave the most unpredictably (have large variance).
  or the opposite? states that we can be certain about.
- state-actions with large effect. a large change in state
- actions with disentangled effects (leaving other state dimension invariant)
- states / actions that allow efficient traversals. (fewer actions required to get from A to B)
- find actions that have minimal, yet predictable effect (for reductionism).

***

Surprise has the inductive bias that it wants to explore noisy states first. The nosier the better, as they are the least predictable.
This doesnt seem like a useful inductive bias...

***

exploration strategies

- optimisim in the face of uncertainty
- intrinsic motivation
  - surprise (rewarded for seeing unpredictable states)
  - novelty (rewarded for seeing new states)
- count based
  - and pseudo count?
- max entropy

***

I expect that intrinsic motivation exploration strategies will be highly dependent on their past.
If it sees a few rewards for doing X, then it will continue to explore within X, possibly getting more rewards.
Positive feedback.

Is also dependent on how the value fn approximator generalises the rewards it has seen.


***

$$
\begin{aligned}
P^{\pi}(\tau | \pi) = d_0(s_0) \Pi_{t=0}^{\infty} \pi(a_t | s_t)P(s_{t+1} | s_t, a_t) \\
d^{\pi}(s, t) = \sum_{\text{all $\tau$ with $s = s_t$}}P^{\pi}(\tau | \pi) \\
d^{\pi}(s) = (1-\gamma)\sum_{t=0}^{\infty} \gamma^t d^{\pi}(s, t) \\
\end{aligned}
$$

Does this discounted state distribution really make sense???

Convergence
$$
KL(d^{\pi}(s, t), d^{\pi}(s))
$$


***

- note. but we dont just care about exploring states??? inductive bias in state-actions?



***

Relationship to the heat equation?!
$$
\frac{\partial d}{\partial t} = \alpha \frac{\partial^2 d}{\partial s^2}
$$

***

Want: for a given finite time horizon, the state visitation distribution is approimately max entropy. If we only require convergence in the limit, we could

Also, algorithms with short memories may forget.


***



## What do we require from an exploration strategy?

- Non-zero probability of reaching all states, and trying all actions in each state.

Nice to have

- Converges to a uniform distribution over states.
- Scales sub-linearly with states
- Samples according to uncertainty.
