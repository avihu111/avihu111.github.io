---
layout: post
title: Active Learning on a Budget
---

This blogpost provides an intuitive overview of our paper "Active Learning on a Budget - Opposite Strategies Suit High and Low Budgets".
[Our paper can be viewed here](https://arxiv.org/abs/2202.02794).
This work was done in collaboration with [Guy Hacohen](https://www.cs.huji.ac.il/w~guy.hacohen/) and Prof [Daphna Weinshall](https://www.cs.huji.ac.il/~daphna/)

### Motivation:
Consider for example, that you started a startup company that uses satellite images to monitor crops automatically, and segment every pixel in the image to "healthy/unhealthy". Your system would assist the farmers to monitor their crops much more efficiently.


As you are familiar with machine learning and computer vision tasks, you may wish to train some convolutional neural network to solve this task. This is a supervised learning problem: mapping from the images into the segmentation map. 

![image](https://user-images.githubusercontent.com/39214195/160606321-33e13805-78fc-4b8a-b98c-4c8a9cb09ab7.png)

The satellite images are already collected, However, the segmentation task requires a human expert (perhaps a geologist) to assess, and might be very expensive. Therefore, your budget only allows you to hire a geologist to segment 500 images. You wish to get as much as you can from these segmentations. Specifically, you want to select the most informative samples, that would improve your model's generalization. 

The naïve thing would be to randomly select samples to annotate, but we know we can do better than that. This is where Active Learning comes to play.

### Active Learning:
In active learning, the model selects the samples to be labeled. Classical works in active learning focus on the high budget settings, which is the case when you have already many labeled samples, and with to query even more.

One classic method in active learning is called uncertainty sampling: we select the sample whose confidence score (measured by the highest softmax response) is the lowest. That way, we label a sample that is the most informative to the model.
Another important principle in active learning is diversity: In case we select a batch of samples, we wish that the samples would not be correlated with each other. Taking similar samples would reduce cause an unused annotation. One example to such a method is "Coreset", which uses the features in the penultimate of the network, and attempts to select the samples with the farthest features from the labeled samples iteratively.


### Limitations of classic Active Learning methods
Notice that the methods discussed above rely on an existing trained model. Therefore, these methods cannot be used for selecting the initial pool of samples. On top of that, when given a low number of samples, these methods usually fail to improve over random selection.


### TypiClust
