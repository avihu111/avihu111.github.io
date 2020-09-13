---
layout: post
title: Tax optimisation as an adversarial game
---

I came across a new adversarial setting (at least new to me...) while getting curious about how to apply AI/ML to complete tax law and how to regulate tax optimisation (aka avoidance in my mind, sigh).

Some background:

> NZ uses a General Anti-Avoidance Rule (GAAR). In practice this means judiciaries end up making the law. Aka, there doesnt exist a set of well-specified laws, so judges must interpret the GAAR (expensive calls to the oracle). The rulings are then used as precedent for the future.

Ok, so this post focuses on two ideas.

<side>but if we could classify all types of avoidance then tax law would be complete??? kinda? ...</side>

- Completing tax law. (a formal set of rules)
- <a href="#classifying-tax-avoidance">Classifying avoidance</a>. (precedent and judges interpretation)

## Completing tax law

Current tax law is incomplete, we have an incomplete approximation of some ideal knot of tax laws, tied in a bow. To complete these tax laws, we query judiciaries and they act as our oracles.

Worse than than, in our attempt to build this ideal, complete knot of tax laws. Mistakes have been made, where behaviours deemed illegal by the ideal tax laws, are deemed legal, by current tax laws.

<!-- We occasionally see these cases resolved by a high court. -->

These loopholes in the ideal tax knot are what private accountants and lawyers like to pull on.

### Adversarial accounting

Imagine a game between [IRD](https://www.ird.govt.nz/) and a taxpayer (from the perspective of IRD)

<side>Huh, a property of the minima (wrt to IRDs actions) would be that dLoss/dAdversary would also = 0. It doesnt matter what the adversary does, our loss remains the same, tax cant be avoided (although maybe they could pay more than necessary). Is that because it is a zero-sum game?</side>

1. You reveal your move (write and pass some tax legislation).
2. Your adversary (a taxpayer) organises its finances to minimise tax paid (legally or not, that is a question for another day).
3. The loss is revealed to the adversary (they avoided $x$ dollars of tax). You recieve the aggregated loss over all taxpayers (you can choose to query an expensive oracle -- judges/court -- to check whether your adversary has made a legal move).


What makes this setting hard?
- __Information asymmetry__. The adversary always gets access to the current loss (they know how much tax they avoided -- at least with respect to their moves). While you only get access to the aggregated loss. But you can get info about specific taxpayers if you query an expensive oracle (audits/jidiciaries).
- __Computation asymmetry__. The adversary needs to find a single strategy to minimise tax (that is also legal). You need to find and subvert all potential strategies to avoid paying tax.
- __Memory asymmetry__. In the multiplayer setting (many adversaries), it is easy(er) for each adversary to model and track changes in tax law (maybe they even collaborate...). But it is hard(er) for you to model and track changes to thousands, or more, adversaries (changes in a corporations finances my be indicative of avoidance).

<!-- ### Related: Adversarial examples

What if you know that your adversary is going to be given your current model? How would you design your model given that information? Design a model so that it is stable under an informed adversarial attack. -->

So, in summary. There is a target function (implicitly known by judges in the high court), which captures tax law. We are trying to approximate that implicit knowledge with few samples. But, as we learn our approximation, our adversaries (taxpayers) adapt their strategies. So, while learning to approximate the target function we also want to minimise the taxpayers ability to exploit our approximation.

Questions raised;

* How can we design an un-exploitable model, or what might be called a complete set of laws?
* How much harder is it to design un-exploitable systems than to game that system?
* How would you even prove that a strategy is un-exploitable?
* Is there a measure of gameability? The ability to __easily__ find hacks/strategies?
* This can actually be formalised as the 'regret' of the tax laws. This raises the question of; how the losses of the adaptive policies that are used to set tax law have scaled in the past.

## Classifying tax avoidance

Tax avoidance is defined as (in my own words); when a law is used in a way that was not intended.



when the finances of an individual / company are organised in a way

<!-- set of actions that are legal, but when viewed from a 'global' perspective, their only purpose is to avoid tax. -->

The more formal version is:

### Credit assignment

So if we have a set of loss function that capture the tax payers goals, ... and tax minimisation.

Can we assign credit to certain actions? WANT: this (set of) action(s) minimised tax paid, and did little else. (in fact, it made the company have a more complex structure and harder to run everyday.)

***

TODO. Relationship to reinforcement learning and reward hacking? And some of the safety research?
https://worldmodels.github.io/
Overfitting to approximate models, can do well in the virtual world, but poorly in the real world.




Legal questions

* What if I am overpaying my tax and then reorganise my finances so that I now pay as much as I 'should' be.
* The law seems to be two things? Specifies (some of) the allowable moves, a classifier of avoidance.
