---
layout: post
title: Active Learning on a Budget
---

This blogpost provides an intuitive overview of our paper "**Active Learning on a Budget - Opposite Strategies Suit High and Low Budgets"**.
[Our paper can be viewed here](https://arxiv.org/abs/2202.02794).

This work was done in collaboration with [Guy Hacohen](https://www.cs.huji.ac.il/w~guy.hacohen/) and Prof [Daphna Weinshall](https://www.cs.huji.ac.il/~daphna/)

## Introduction
One of the key challenges in supervised deep learning is its reliance on a large number of labeled samples. In many practical setups, the annotation process is  expensive, and is the bottleneck in improving performance. For example, in medical imaging analysis the annotations require experts (a radiologist) annotations, whose time is very costly. **Semi-supervised** and **self-supervised** learning attempts to utilize the unlabeled data to improve the model's generalization.

### Active Learning
In active learning, **the model selects the samples to be labeled**. In common setups, the model gets a **budget**, which is the number of samples it can send to annotation. Classical works in active learning focus on the high budget settings, which is the case when you have already many labeled samples, and wish to query even more.

<img src="https://user-images.githubusercontent.com/39214195/160649574-177598a1-d493-46f2-8e70-cd20fe9d5342.png" width="440">

Active learning methods are usually consist of two principles:
1. **Uncertainty Sampling** - selecting the samples that the model is the most unsure of their predictions. Annotating these samples would be most informative to the model. One way to measure uncertainty is by the maximal Softmax response. 
2. **Diversity** - when selecting a batch of samples, we wish that the samples would not be correlated with each other. One approach is [Coreset](https://arxiv.org/abs/1708.00489), which uses the features in the penultimate of the network, and attempts to select the samples with the farthest features from the labeled samples iteratively.
 

### Low Budget Active learning
The methods discussed above rely on an existing trained model and therefore cannot be used for selecting the initial pool of samples. On top of that, when given a low number of samples, these methods usually fail to improve over random selection.
There might be several causes to this:
1. Models trained only with few samples are prone to overfit, and tend to have over-confident predictions. Relying on these predictions, can lead to a noisy uncertainty signal.
2. The field of curriculum learning shows that starting from "easy" samples and gradually increasing the "difficulty" level is preferable. The analogous idea in active learning is selecting "easy" samples when the budget is low, and "hard" samples when the budget is high.

## Our contributions
### Phase transition - From low to high budget 
Our paper shows that indeed selecting "easy" samples is more beneficial in low budgets, and sampling "hard" samples is beneficial when the budget is high. 
We provide a theoretical framework that assumes that the "error function" (a function of "expected error" vs budget) is exponentially decreasing. Assuming two independent learners with differently paced exponential error functions, we prove that there is a shift in the optimal sampling strategies. 
The expected phenomenon from the theoretical analysis:

<img src="https://user-images.githubusercontent.com/39214195/161326240-bd3e5c38-4548-4655-9ee5-bf5ff3eb6bf4.png" width=440>

The empirical result we see is similar:

<img src="https://user-images.githubusercontent.com/39214195/161326609-a60ff5fc-ca97-4fdb-bd28-5a5468c2499c.png" width=440>


### TypiClust
TypiClust (Typical Clustering) is a Low Budget Active Learning that improves generalization by a large margin.
It attempts to select typical samples (dense regions in the data distribution) - intuitively, these samples would cover most of the data distribution.
Typicality is defined as the inverse of mean distance of the samples to its K nearest neighbors:
<img src="https://render.githubusercontent.com/render/math?math=\bigg(\frac{1}{K}\sum_{x_i\in \text{K-NN}(x)}||x-x_i||_2\bigg)^{-1}" width=300>


TypiClust consists of 3 steps:
1. **Unsupervised Learning** - Employing some self supervised model to create semantically meaningful features. We trained SimCLR when experimenting on CIFAR and TinyImageNet, and used DINO pretrained features on ImageNet.
2. **Clustering** - To enforce diversity in the selected samples, we cluster the data to N + B (N is the number of labeled samples, B is the budget size). We then select the B largest clusters that do not contain a labeled image.
3. **Typical Selection** - From those B clusters, we select the most typical sample.


<img src="https://user-images.githubusercontent.com/39214195/160614551-99e874e4-2ffd-4a48-baea-8e4232b5ed2a.png" width="440">

TypiClust sample selection shows clear improvements in performance on a variety of datasets and in many training frameworks:
<img src="https://user-images.githubusercontent.com/39214195/160615739-31fb1135-6f84-435a-beb6-95ca6d8c028d.png" width="440">


## Why it works
Our method prefers to sample dense parts of the distribution, which is more beneficial when labeled data is sparse.
We compare the selection of 10 samples on synthetic data of our method versus coreset:

<img src="https://user-images.githubusercontent.com/39214195/160669776-ccc8e7a8-0df3-4c7a-9a75-5df01a051d1f.gif" width="440">

<img src="https://user-images.githubusercontent.com/39214195/160630431-930a52df-69e6-4219-9183-9fc8aeac84b9.gif" width="440">

Notice that coreset selects the outliers in the distribution, while our method selects dense regions, while keeping the selection diverse.

