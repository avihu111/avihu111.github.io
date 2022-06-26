---
layout: post
title: Active Learning on a Budget
---

This blogpost provides an overview of our paper "**Active Learning on a Budget - Opposite Strategies Suit High and Low Budgets"**.


This work was done in collaboration with [Guy Hacohen](https://www.cs.huji.ac.il/w~guy.hacohen/) and Prof [Daphna Weinshall](https://www.cs.huji.ac.il/~daphna/).

[Our paper can be found here](https://arxiv.org/abs/2202.02794).

[Our GitHub implementation can be found here](https://github.com/avihu111/TypiClust).

[A Twitter thread can be found here](https://twitter.com/AvihuDkl/status/1529385835694637058).


## Introduction
One of the key challenges in supervised deep learning is its reliance on a large number of labeled examples. In many practical setups, the annotation process is  expensive, and becomes the bottleneck in improving performance. For example, in medical imaging analysis the annotations require an expert (a radiologist), whose time is very costly.

### Active Learning
In active learning, **the model selects the samples to be labeled**. In common setups, the model gets a **budget**, which is the number of samples it can send for annotation. 

<img src="https://user-images.githubusercontent.com/39214195/175805064-6c8a9bfa-6ad6-4976-96c2-7cf407e90adf.png" width="540">

Active learning methods usually follow two principles:
1. **Uncertainty Sampling** - selecting the samples that the model is most unsure regarding their predictions. Annotating these samples would be most informative to the model. One way to measure uncertainty is by the maximal Softmax response. 
2. **Diversity** - when selecting a batch of samples, it is beneficial to choose uncorrelated examples (as much as possible). One approach is [Coreset](https://arxiv.org/abs/1708.00489): it uses the features in the penultimate layer of a deep network, and attempts to select the samples that lie as far as possible from the already labeled samples; this is repeated iteratively.
 

### "Cold Start" in Low Budgets
Classical works in active learning focus on the high budget settings, which is the case when you have already many labeled samples, and wish to query even more.
However, **when given a low number of samples, these methods usually fail to improve over the simple random label selection**.
There might be several causes to this:
1. Models trained only with few samples are prone to overfit, and tend to have over-confident predictions. Relying on these predictions typically leads to a noisy uncertainty signal.
2. The field of curriculum learning shows that starting from "easy" samples and gradually increasing the "difficulty" level is preferable. The analogous insight in active learning is selecting "easy" samples when the budget is low, and "hard" samples when the budget is high.

## Phase transition - From low to high budget 
Our paper provides an explanation for the cold start phenomenon and suggests a novel active learning method, suitable for low budgets.

In high budgets, we already knew that sampling uncertain (or ambivalent) was a successful active learning strategy - this is what uncertainty sampling is all about. Our paper shows that in low budgets, the **opposite** strategy is actually much better. In low budgets, is it preferable to sample the most representative and least ambivalent samples. In other words (as the paper name suggests), **opposite strategies suit high and low budgets**. 
The following figure summarizes this rather intuitive finding:

<img src="https://user-images.githubusercontent.com/39214195/175804983-dd821815-1b9b-4b2f-ac5e-2d42114cd42f.png" width=640>

We provide a theoretical model for active learning (which we will get into the details later on). In this model, we prove that as the budget size increases, the optimal sampling strategy changes. 
The theoretical analysis predicts the following behaviour:

<img src="https://user-images.githubusercontent.com/39214195/175806623-c69b6a8a-d779-47fc-8272-9c4cebf864da.png" width=440>

Let's analyze what we see here:
- We plot different strategies as a function of the budget size.
- We look at the accuracy difference from random selection. Every Active learning attempts to improve over random selection. So a beneficial active learning strategy should be above random selection. 
- In low budgets, sampling the common regions improves random selection, and uncertainty sampling is detrimental.
- In high budgets, we see the opposite behaviour: uncertainty sampling works, while the opposite strategy fails.

The empirical results we see give a similar picture. We plot an uncertainty sampling method (Margin) versus a Typical samples selection method (TypiClust) and plot the accuracy difference from random selection. We indeed see a change in the optimal sampling strategy as the budget size increases. 

<img src="https://user-images.githubusercontent.com/39214195/175806635-3dca5ac7-7d19-4b3e-8eb0-fe5b69427cfe.png" width=440>


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
TypiClust samples dense parts of the distribution, which is more beneficial when labeled data is scarce.
We compare the selection of 10 examples on synthetic data, comparing our method with coreset selections:

<img src="https://user-images.githubusercontent.com/39214195/160669776-ccc8e7a8-0df3-4c7a-9a75-5df01a051d1f.gif" width="340"> <img src="https://user-images.githubusercontent.com/39214195/160630431-930a52df-69e6-4219-9183-9fc8aeac84b9.gif" width="340">

Notice that coreset selects outliers (sparse regions), while our method selects examples from dense regions, while keeping the selection diverse.


## Theoretical Analysis

We now elaborate on our theoretical model. We analyse a mixture of two general learners, where one learns a distinct part of the data distribution.
Specifically, we split the data distribution into two distinct regions: $R_1$ and $R_2$, where each region is learned by its own learner. 
Denote $p=Prob(R_1)$, and we can define the data distribution $D$ as a mixture of the two distributions $D_1,D_2$. 

We now define the error score function as follows: for every $m$, the error score returns the expected generalization error of a model trained on $m$ randomly selected samples. 
Now, assume that $R_1$ is learned faster than $R_2$. This means that its error score decreases faster: 

$$\forall m : E_1(m) < E_2(m) $$

We describe this by a pacing parameter $\alpha < 1$ where:

$$E_2(m) = E_1(\alpha m)=E(\alpha m) $$

Intuitively, if $\alpha=0.5$, then if we have 1000 samples from $R_2$, we should expect the generalization error of 500 samples from $R_1$. 

What does this have to do with active learning? Well, we can now view active learning as **biased sampling**.
In active learning, we replace random selection with a clever sampling strategy, hoping to improve generalization. 
In this model, we can either oversample $R_1$ or oversample $R_2$. 
If we sample randomly, the expected error is:

$$ Er_{total}(m) = pE(pm)+(1-p)E(\alpha (1-p)m) $$

We know that $R_1$ is learned faster than $R_2$ (it's error decreases faster). So, which region should we oversample?
Oversampling can be viewed in the following way:

$$ m_1=pm+\Delta $$

$$ m_2=(1-p)m-\Delta $$

- If $\Delta>0$ we oversample $R_1$. 
- If $\Delta<0$ we oversample $R_2$. 
- If $\Delta=0$ we simply use random selection. 

We then prove the following theorems:
### Theorem 1
The optimal strategy can be determined by a threshold test:

$$ \frac{E'(pm)}{E'(\alpha (1-p)m)} < \frac{\alpha (1-p)}{p} \Longrightarrow \text{Oversample } R_1 $$

So, assuming we know the error score and its derivative, we can decide what is the optimal thing to do. 


### Theorem 2
If $E(m)$ is smooth, monotone and bounded by an exponential, then it presents the phase transition (it is undulating). 
