---
layout: post
title: Active Learning Through a Covering Lens
---

This blogpost discusses our paper NeurIPS 2022 paper: "**Active Learning Through a Covering Lens"**.

This work was done in collaboration Ofer Yehuda, [Guy Hacohen](https://www.cs.huji.ac.il/w~guy.hacohen/) and Prof [Daphna Weinshall](https://www.cs.huji.ac.il/~daphna/).

[The paper can be viewed using this link](https://arxiv.org/abs/2205.11320).

## Introduction

In our [Previous Post](https://avihu111.github.io/Active-Learning/) we claimed that in low budgets, it is better to prefer typical (representative) samples. 
This was in contrast to high budgets, where it is better to prefer ambivalent samples. 
We also saw TypiClust, an active learning method that clusters the data and selects the densest sample from every cluster. 

## ProbCover

In this paper, we formalize active learning as a Probability Coverage problem. That is, we attempt to maximize the probability mass that is labeled. 
Specifically, given some ball radius $\delta > 0$ and a budget constraint $b$, we attempt to select a subset of samples $L$ that maximizes probability coverage:

$$\underset{L\subseteq X,\left|L\right|=b}{\text{argmax}}P\left(\bigcup_{x\in L}B_{\delta}\left(x\right)\right) $$

We define the purity of a $\delta$ as the ratio of points that have pure $\delta$ balls - that their $\delta$ ball consists of only one label.

$$\pi(\delta)=P\left(\{ x:B_{\delta}\left(x\right)\text{ is pure}\} \right)$$

We show that when using the 1-Nearest-Neighbor algorithm, we can bound the generalization error as follows:

$$L_{D}\left(\hat{f}\right)\le\left(1-P\left(x\in C\right)\right)+\left(1-\pi\left(\delta\right)\right) $$

This introduces a tradeoff: as $\delta$ increases, the coverage increases, but the purity decreases.

As we don't have the true probability distribution, we can approximate this by using the empirical distribution. 
Under the empirical distribution, the problem can be reduced into a MaxCoverage - attempting to maximize the number of covered samples in the dataset.
MaxCoverage is a known NP-Hard problem, which has a greedy algorithm that achieves $1-\frac{1}{e}$ approximation).


## Connections with Coreset
Coreset attempts to minimize the farthest distance from a sample labeled set to a sample from the unlabeled set.
This objective is equivalent to enforce complete coverage, while minimizing the ball radius. 
<img src="https://user-images.githubusercontent.com/39214195/194885805-53863068-36ba-44f6-8b48-d8a32ef1d052.png" width="540">

In contrast, ProbCover enforces ball radius, and attempts to maximize coverage. 

<img src="https://user-images.githubusercontent.com/39214195/194886947-cb459de9-3ef5-4597-b9ba-c53b4fd89885.png" width="540">

Notice that Coreset selects outliers of the data distribution, while ProbCover selects - selected points are colored by their density (red is high density, while yellow is low density). 

## Results
In datasets where clustering algorithms struggle (e.g CIFAR-100 and Tiny-ImageNet), this approach shows significant improvements over TypiClust.

<img src="https://user-images.githubusercontent.com/39214195/175888064-fae0b2de-cd4e-46ef-b2eb-a9352f21602e.png" width="640">

