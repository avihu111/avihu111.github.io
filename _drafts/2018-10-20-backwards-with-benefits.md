---
layout: post
title: The advantages of backward reasoning?
---

What can I gain if, rather than planning into the future, I can plan backwards from my goals (or both)?

_(I imagine these ideas are well known within the planning/AI, automated-proving, graph and tree-search communities. But I just needed to get these ideas out of my head.)_

Sepcifically, I am interested in; what problems can we solve more efficiently if we (also) have access to an inverse model of the dynamics of a system. This question was inspired by !!!?.

Let the state of a system be $s_t$ and how it changes over time be described by the transition function, $s_{t+1} = \tau(s_t)$. Typical approaches to planning (at least in reinforcement learning) involve using the transition function to simulate future events.

What if I also knew $\tau^{-1}(s_{t+1})$? Can I learn to solve problems that I previously couldnt?

***

Let's consider the U-problem. Imagine the goal is to get from $A$ to $B$ (left). However, the learner has been given a shaped reward, $r$, encouraging smaller distances between the agent 's position, $p$ and $B$. $r_t = -\parallel p_t - \mathcal B \parallel + 1_{p=\mathcal B}$.

![]({{ site.baseurl }}/images/abduction-cup.png){: .center-image }

This shaped reward makes learning impossible for many well known reinforcment learning algorithms (Q, PG, ...).The agent will quickly get stuck in the U, optimising short term rewards

(Even planning might not help)

## Deduction + Abduction

Imagine you are an agent in a 2d environment. Lets discretise the space for simlicity, he grid graph in n dimensions.

However, rather than having access to the usual euclidean metric, you have nothing.
You are tasked with getting from A to B.

This is a common situation in the RL setting. The learner does not know how its actions move it around the space it is in, and it does not know which actions will lead to a reward.


![]({{ site.baseurl }}/images/abduction.png){: .center-image }


In this case, in the deduction only case, there are $2^6$ states that are searched. While in the adbuction and deduction search there are $2\times 2^3=2^4$ states searched.

![]({{ site.baseurl }}/images/abduction-tree.png){: .center-image }

## Questions/thoughts

- If you have many subgoals along the way.
- Requires that assumption of symmetry. All we are doing is exploiting the symmetry that the path from A to B is the same as the path from B to A. (when is this not the case?)
