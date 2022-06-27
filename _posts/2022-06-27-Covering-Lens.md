---
layout: post
title: Active Learning Through a Covering Lens
---

This blogpost discusses our paper "**Active Learning Through a Covering Lens"**.

This work was done in collaboration Ofer Yehuda, [Guy Hacohen](https://www.cs.huji.ac.il/w~guy.hacohen/) and Prof [Daphna Weinshall](https://www.cs.huji.ac.il/~daphna/).

[The paper can be viewed using this link](https://arxiv.org/abs/2205.11320).

## Introduction

In our [Previous Post](https://avihu111.github.io/Active-Learning/) we claimed that in low budgets, it is better to prefer typical (representative) samples. 
This was in contrast to high budgets, where it is better to prefer ambivalent samples. 
We also saw TypiClust, an active learning method that clusters the data and selects the densest sample from every cluster. 


## Max Coverage

We formalize active learning as a Probablity Coverage problem. That is, we attempt to maximize the probability mass that is labeled. 
Specifically, given some ball radius $\delta > 0$ and a budget constraint $b$, we attempt to select a subset of samples $L$ that maximizes probability coverage:
$$\underset{L\subseteq X,\left|L\right|=b}{\text{argmax}}P\left(\bigcup_{x\in L}B_{\delta}\left(x\right)\right) $$

Clearly, the larger the $\delta$, we can cover a larger probability mass - but that can make the problem trivial. 
To solve this, we define the purity of a $\delta$ as the ratio of point that have pure $\delta$ balls - that their $\delta$ ball consists of only one label.
$$\pi(\delta)=P\left(\{ x:B_{\delta}\left(x\right)\text{ is pure}\} \right)$$

We show that when using the 1-Nearest-Neighbor algorithm, we can bound the generalization error as follows:
$$L_{D}\left(\hat{f}\right)\le\left(1-P\left(x\in C\right)\right)+\left(1-\pi\left(\delta\right)\right) $$
This introduces a tradeoff: as $\delta$ increases, the coverage increases, but the purity decreases.

As we don't have the true probability distribution, we can approximate this by using the empirical distribution. 
Hence, our problem can be reduced into a MaxCoverage problem - attempting to maximize the number of covered samples in the dataset.


## Connections with Coreset
Coreset enforces complete coverage, while minimizing the ball radius (hence the purity).
However, MaxCoverage enforces ball radius, and attempts to maximize coverage. 
<img src="https://user-images.githubusercontent.com/39214195/175887920-dec4cd9a-547b-4ea5-a312-f21066ba82e6.png" width="540">


## Results
In datasets where clustering algorithms struggle (e.g CIFAR-100 and Tiny-ImageNet), this approach shows significant improvements over TypiClust.

<img src="https://user-images.githubusercontent.com/39214195/175888064-fae0b2de-cd4e-46ef-b2eb-a9352f21602e.png" width="540">


