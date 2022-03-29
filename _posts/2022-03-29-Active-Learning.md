---
layout: post
title: Active Learning on a Budget
---

This blogpost provides an intuitive overview of our paper "**Active Learning on a Budget - Opposite Strategies Suit High and Low Budgets"**.
[Our paper can be viewed here](https://arxiv.org/abs/2202.02794).
This work was done in collaboration with [Guy Hacohen](https://www.cs.huji.ac.il/w~guy.hacohen/) and Prof [Daphna Weinshall](https://www.cs.huji.ac.il/~daphna/)

## Introduction
One of the key challenges in supervised deep learning is its reliance on a large number of labeled samples. In many practical setups, the annotation process is very expensive, and is the bottleneck in improving performance. For example, in medical imaging analysis the annotations require experts (a radiologist) annotations, whose time is very costly. **Semi-supervised** and **self-supervised** learning attempt to utilize the unlabeled data to improve the model's generalization.

## Active Learning
In active learning, **the model selects the samples to be labeled**. In common setups, the model gets a **budget**, which is the number of samples in can send to annotation. Classical works in active learning focus on the high budget settings, which is the case when you have already many labeled samples, and with to query even more.  Active learning methods are usually consist of two principles:
	1. **Uncertainty Sampling** - selecting the samples that the model the most unsure over their predictions. Annotating these samples would be most informative to the model. One way to measure uncertainty is by the maximal Softmax response. 
	2. **Diversity** - when selecting a batch of samples, we wish that the samples would not be correlated with each other. One approach is [Coreset](https://arxiv.org/abs/1708.00489), which uses the features in the penultimate of the network, and attempts to select the samples with the farthest features from the labeled samples iteratively. (add graph on 2d GMM dataset??)
  
### Low Budget Active learning
Notice that the methods discussed above rely on an existing trained model. Therefore, these methods cannot be used for selecting the initial pool of samples. On top of that, when given a low number of samples, these methods usually fail to improve over random selection.
There might be several causes to this:
1. Models trained only with few samples are prone to overfit, and tend to have over-confident predictions. Relying on these predictions, can lead to a noisy uncertainty signal.
2. The field of curriculum learning shows that starting from "easy" samples and gradually increasing the "difficulty" level is preferable. The analogous idea in active learning, is selecting "easy" samples when the budget is low, and "hard" samples when the budget is high.


## TypiClust
TypiClust (Typical Clustering) is a Low Budget Active Learning, that improves generalization by a large margin.

It consists of 3 steps:
1. **Unsupervised Learning** - Emplpoying some self supervised model to create semantically meaningful features
2. **Clustering** - Clustering the features to the number of samples we wish to add
3. **Typical Selection** - selecting the most dense feature from every cluster

<!-- ![image](https://user-images.githubusercontent.com/39214195/160614551-99e874e4-2ffd-4a48-baea-8e4232b5ed2a.png|width=100) -->
<img src="https://user-images.githubusercontent.com/39214195/160614551-99e874e4-2ffd-4a48-baea-8e4232b5ed2a.png" width="640">

