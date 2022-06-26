---
layout: post
title: Active Learning on a Budget
---

This blogpost provides an intuitive overview of our paper "**Active Learning on a Budget - Opposite Strategies Suit High and Low Budgets"**.


This work was done in collaboration with [Guy Hacohen](https://www.cs.huji.ac.il/w~guy.hacohen/) and Prof [Daphna Weinshall](https://www.cs.huji.ac.il/~daphna/).

[Our paper can be found here](https://arxiv.org/abs/2202.02794).

[Our GitHub implementation can be found here](https://github.com/avihu111/TypiClust).

[A Twitter thread can be found here](https://twitter.com/AvihuDkl/status/1529385835694637058).


## Introduction
One of the key challenges in supervised deep learning is its reliance on a large number of labeled examples. In many practical setups, the annotation process is  expensive, and becomes the bottleneck in improving performance. For example, in medical imaging analysis the annotations require an expert (a radiologist), whose time is very costly.

### Active Learning
In active learning, **the model selects the samples to be labeled**. In common setups, the model gets a **budget**, which is the number of samples it can send for annotation. Classical works in active learning focus on the high budget settings, which is the case when you have already many labeled samples, and wish to query even more.

<img src="https://user-images.githubusercontent.com/39214195/160649574-177598a1-d493-46f2-8e70-cd20fe9d5342.png" width="540">

Active learning methods usually follow two principles:
1. **Uncertainty Sampling** - selecting the samples that the model is most unsure regarding their predictions. Annotating these samples would be most informative to the model. One way to measure uncertainty is by the maximal Softmax response. 
2. **Diversity** - when selecting a batch of samples, it is beneficial to choose unccorrelated examples (as much as possible). One approach is [Coreset](https://arxiv.org/abs/1708.00489): it uses the features in the penultimate layer of a deep network, and attempts to select the samples that lie as far as possible from the already labeled samples; this is repeated iteratively.
 

### "Cold Start" in Low Budgets
The principles discussed above rely on an existing trained model and therefore cannot be used for selecting the initial pool of samples. On top of that, **when given a low number of samples, these methods usually fail to improve over mere random label selection**.
There might be several causes to this:
1. Models trained only with few samples are prone to overfit, and tend to have over-confident predictions. Relying on these predictions typically leads to a noisy uncertainty signal.
2. The field of curriculum learning shows that starting from "easy" samples and gradually increasing the "difficulty" level is preferable. The analogous insight in active learning is selecting "easy" samples when the budget is low, and "hard" samples when the budget is high.

## Phase transition - From low to high budget 
We provide an explanation for the cold start phenomenon and suggest a novel active learning method, suitable for low budgets.
Our paper shows that selecting easy/common/representative samples is more beneficial in low budgets, and selecting hard/unrepresentative/uncertain samples is beneficial when the budget is high. 

We provide a theoretical model for active learning (which we will get into later on). In this model, we prove that there is a shift in the optimal sampling strategies. 
The theoretical analysis predicts the following behaviour:

<img src="https://user-images.githubusercontent.com/39214195/161326240-bd3e5c38-4548-4655-9ee5-bf5ff3eb6bf4.png" width=440>

Let's analyze what we see here:
- We plot different strategies as a function of the budget size.
- We look at the accuracy difference from random selection. Every Active learning attempts to improve over random selection. So a beneficial active learning strategy should be above random selection. 
- In low budgets, sampling the common regions improves random selection, and uncertainty sampling is detrimental.
- In high budgets, we see the oposite behaviour: uncertainty sampling works, while the oposite strategy fails.

The empirical results we see give a similar picture. We plot the performance of Margin (an uncertainty sampling method) versus TypiClust (our method, which we describe below).

<img src="https://user-images.githubusercontent.com/39214195/161326609-a60ff5fc-ca97-4fdb-bd28-5a5468c2499c.png" width=440>


## TypiClust
TypiClust (Typical Clustering) is a low budget active learning method that improves generalization by a large margin.
It attempts to select typical samples (dense regions in the data distribution) - intuitively, these samples would cover more of the data distribution, as compared to random samples.
Typicality is defined as the inverse of the mean distance of an example to its K nearest neighbors:
<img src="https://render.githubusercontent.com/render/math?math=\bigg(\frac{1}{K}\sum_{x_i\in \text{K-NN}(x)}||x-x_i||_2\bigg)^{-1}" width=300>


TypiClust consists of 3 steps:
1. **Unsupervised Learning** - Employing some self supervised model to create semantically meaningful features. We trained SimCLR when working with CIFAR and TinyImageNet, and used DINO pretrained features on ImageNet.
2. **Clustering** - To enforce diversity in the selected sample, we cluster the data to N + B (N is the number of labeled examples, B is the budget size). We then select the B largest clusters that do not contain a labeled example.
3. **Typical Selection** - From each of those B clusters, we select the most typical example.


<img src="https://user-images.githubusercontent.com/39214195/160614551-99e874e4-2ffd-4a48-baea-8e4232b5ed2a.png" width="640">

### Results
TypiClust sample selection shows clear improvements in performance on a variety of datasets and in many training frameworks:

<img src="https://user-images.githubusercontent.com/39214195/160615739-31fb1135-6f84-435a-beb6-95ca6d8c028d.png" width="440">

Notice that the improvements are more significant when using semi supervised learning. Specifically, when selecting 10 samples from CIFAR-10, TypiClust achieves a significant improvement of 39.4% in test accuracy over random selection.

### Visualization
TypiClust samples dense parts of the distribution, which is more beneficial when labeled data is scarse.
We compare the selection of 10 examples on synthetic data, comparing our method with coreset selections:

<img src="https://user-images.githubusercontent.com/39214195/160669776-ccc8e7a8-0df3-4c7a-9a75-5df01a051d1f.gif" width="440">

<img src="https://user-images.githubusercontent.com/39214195/160630431-930a52df-69e6-4219-9183-9fc8aeac84b9.gif" width="440">

Notice that coreset selects outliers (sparse regions), while our method selects examples from dense regions, while keeping the selection diverse.
