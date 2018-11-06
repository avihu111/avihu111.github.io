---
layout: post
title: Real time bandits
---

Imagine you want to make a twitter bot that is rewarded by likes (or more likely, to write papers that are rewarded by many citations). This is reinforcement learning as we have a non-differentiable incentive. But, there is extra structure.

Unlike the typical setting, where the reward is recieved after $n$ actions (for example at the completion of a game of Go). In this setting, we are told which action is responsible for the reward. The tweet that was liked, the paper that was cited (thus we don't need to assign credit to actions). But we have only received a partial view of the reward. Rather than revealing the true reward of your action, its expected cumulative reward in the future, we only have access to the rewards recieved so far.

> (In contrast to pong) If I receive a reward of 0.7, because I won 7 out of 10 games of pong using policy $\pi$, then in the future I would expect to continue to receive a reward roughly equal 0.7 unless something changes (such as the opponent).
In this setting, receiving a reward of 0.7 at time step $t$ is not indicative of future rewards as your tweet/paper might go 'viral' at some future time, $T$.

## A slow oracle

We could think of this oracle as one that:

> is testing/evaluating your action and gives you feedback based on its current (possibly imperfect) knowledge.

How could this oracle be structured? Some constraints that might be interesting;

- the oracle has some finite computational power and no memory and is searching through the past for useful actions. When it finds good actions it rewards them.
- the oracle has a bias towards recent actions (twitter and academia both seem to share this)
- the rewards given are monotonic (in the case of likes and citiations, we could probably assume that, although it isnt quite true)
- as time tends to infinity, the reward given by the oracle converges to a finite value.

## Formal setting

<side>huh, there are no inputs. No observations. This doesn't seem important (?).</side>
At each time step, $t \in \mathbb Z^+$, you take an action $a^t \in A$ an recieve a reward $r^t \in \mathbb R^t$ (the size of the reward vector changes with $t$). The goal is to learn a poilcy $\pi$ that receives past actions and rewards that maximises $R^{\infty}$.

$$
a^{t+1} = \pi(a^{t:0}, r^{t:0}) \\
R^{\infty} = \mathop{\mathbb E}_{s_t \sim p(\cdot \mid a^{t:0})} \parallel r^i(s_t) \parallel \\
$$

<side>Does thinking about rewards like this help in the markov sequence setting? Where we only get to observe the sum across columns, $\sum_{i=0}^t \textbf{R}^t_i$. $\textbf{Q:}$ How much faster can learning occur when we have access to this information?</side>
We can write the rewards received as a matrix by stacking them horizontally.

$$
\begin{align}
\textbf{R}^t &= \begin{bmatrix}
r_0^0 & r_0^1 & r_0^2 & r_0^3 \\
 & r_1^1 & r_1^2 & r_1^3 \\
 &  & r_2^2 & r_2^3 \\
 &  &  & r_3^3 \\
\end{bmatrix}\\
&= [\text{actions taken} \times \text{time steps}]
\end{align}
$$


Where $r^t_n$ denotes the reward for the $n$th action recived at time $t$.

Note the size of $\textbf{R}^t$. We can imagine a point where we have taken thousands of actions and are receiving feedback on all of them. We may want to integrate this recent feedback with past feedback, which would require $\mathcal O(n^2)$ operations.

So given a time step, how might the rewards be distributed over the past actions?

<img src="../images/real-time-R.png" height="450x" align="middle">

<side>Is there anything dangerous/pathological about doing this?</side>
The velocity ($\Delta R$) and acceleration ($\Delta^2 R$) appear to be useful signals. A simple heurstic could be to maximise them in place of the true $R$.

## Learning

Learning in this setting poses some interesting quirks:

- we __really__ want to pick actions that will be useful in the long term! Ones that continue to pay off (or positively effect the reward of new actions)!
- because the relationship between actions and short term rewards are easier to learn (there is more data) we would expect an agent's behaviour to be myopic (how can this be avoided?)

as well as;

#### Counting on future rewards

<side>How/why is this any different for the usual setting? In the usual setting, the distinction is shitty action this time (give observations, action og another agent, ...)</side>

We need to distinguish between two cases; (1) shitty action, (2) payoff is still in the future.

For example consider the tweet:

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">What do the fourier transform, uncertainty principle and statistical manifolds have in common?<br><br>If I have N samples on a statistical manifold then the more I know about local structure the less I know about global structure.</p>&mdash; Alexander Telfar (@telfarac) <a href="https://twitter.com/telfarac/status/999408963098558464?ref_src=twsrc%5Etfw">May 23, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Does it have no likes because it is uninteresting/not clear/... not a good action? Or because the payoff still lies somewhere in the future. What am I to make of this? It leads to two interesting questions about the explore/exploit trade-off.

- $\textbf{Q:}$ How should we exploit our knowledge of past rewards to update our policy (even if we believe an action has a large payoff coming, but has not yet recieved any reward?).
- $\textbf{Q:}$ How should we explore alternatives given that we may never get a clear answer whether the action was good.

#### Dependence on the future

The reward received, $r_t$, at time, $t$, for the $i$th action (at time $t=i$) is dependent on past and future actions $r^t_i = R(a_t, \dots, a_0)$. This leaves open the possibility that you can take actions now that influence the rewards recieved by past actions. For example, "please retweet X". Heh, or citing your own past work...

<side>Can we make this notion of danger more formal?</side>
This seems very dangerous!! I imagine an agent in this setting would be very likely to start optimising something different to what we wanted as it now has the ability to give itself reward. $\textbf{Q:}$ How can we build a learner is protected against self-gratification? One that doesnt (/minimally) cite itself?
