---
layout: post
title: Active Learning on a Budget
---

This blogpost provides an intuitive overview of our paper "**Active Learning on a Budget - Opposite Strategies Suit High and Low Budgets"**.
[Our paper can be viewed here](https://arxiv.org/abs/2202.02794).

This work was done in collaboration with [Guy Hacohen](https://www.cs.huji.ac.il/w~guy.hacohen/) and Prof [Daphna Weinshall](https://www.cs.huji.ac.il/~daphna/)

## Introduction
One of the key challenges in supervised deep learning is its reliance on a large number of labeled samples. In many practical setups, the annotation process is  expensive, and is the bottleneck in improving performance. For example, in medical imaging analysis the annotations require experts (a radiologist) annotations, whose time is very costly. **Semi-supervised** and **self-supervised** learning attempt to utilize the unlabeled data to improve the model's generalization.

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
It attempts to select typical samples (dense regions in the data distribution) - intuitively, these samples would cover most of the data distribution.
Typicality is defined as the inverse of mean distance of the samples to it's K nearest neighbors:
<img src="https://render.githubusercontent.com/render/math?math=\bigg(\frac{1}{K}\sum_{x_i\in \text{K-NN}(x)}||x-x_i||_2\bigg)^{-1}" width=300>


TypiClust consists of 3 steps:
1. **Unsupervised Learning** - Emplpoying some self supervised model to create semantically meaningful features. We trained SimCLR when experimenting on CIFAR and TinyImageNet, and used DINO pretrained features on ImageNet.
2. **Clustering** - To enforce diversity in the selected samples, we cluster the data to N + B (N is the number of labeled samples, B is the budget size). We then select the B largest clusters that do not contain a labeled image.
3. **Typical Selection** - From those B clusters, we select the most typical sample.


<img src="https://user-images.githubusercontent.com/39214195/160614551-99e874e4-2ffd-4a48-baea-8e4232b5ed2a.png" width="640">

TypiClust sample selection shows clear improvements in performance on a varaity of datasets and in many training frameworks:
<img src="https://user-images.githubusercontent.com/39214195/160615739-31fb1135-6f84-435a-beb6-95ca6d8c028d.png" width="640">


## Why it works
Our method prefers to sample dense parts of the distribution, which is more benefitial when labeled data is sparse.
We compare the selection of 10 samples on synthetic data of our method versus coreset:

<img src="https://user-images.githubusercontent.com/39214195/160630409-9517f632-6c92-4aa2-97a9-e97439a5f889.gif" width="400"> <img src="https://user-images.githubusercontent.com/39214195/160630431-930a52df-69e6-4219-9183-9fc8aeac84b9.gif" width="400">

Notice that coreset selects the ourliers in the distribution, while our method selects dense regions, while keeping the selection diverse.



