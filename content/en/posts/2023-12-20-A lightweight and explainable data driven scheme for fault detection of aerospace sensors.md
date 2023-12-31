---
title: A lightweight and explainable data driven scheme for fault detection of aerospace sensors
date: 2023-12-20
author: Zhongzhi Li
slug: first-post
draft: false
toc: true
categories:
  - test
tags:
  - article
  - English
---

{{<block class="reminder">}}

Article [link](https://ieeexplore.ieee.org/abstract/document/10214393) if you are interested.

{{<end>}}

## Motivation

<p style="text-align: justify;">Aerospace sensors measure the aircraft states including airspeed, attitude angles, load factors, etc. In literature many flight accidents were caused by aerospace sensors fault. It is imminent to develop fault detection and classification (FDC) techniques for the aerospace sensors.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-1.png" width="600">}}

<p style="text-align: justify;">Currently, there are 3 main types of research on this problem. The first is rule-based methods, and the second is model-based detection methods. Researchers first proposed using dynamic process models to analyze input and output signals. The last one is based on data-driven method, which is currently the most popular approach.</p>

<p style="text-align: justify;">However, the above 3 methods still have limitations. First, Detection accuracy needs to be further improved, secondly, Considering practical applications at the edge, the size of the model needs to be reduced, and finally, Increasing the interpretability of data-driven models is necessary.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-2.png" width="600">}}

## Highlights

<p style="text-align: justify;">In response to the current problems of the imagefication-based intelligent FDC researches, we report our most recent advances in this paper. We also summarize the highlights of this paper as fourflod:</p>

<p style="text-align: justify;">knowledge migration: The VGG16 model with pre-trained weights was introduced into the task, which was trained on a large-scale image dataset. By utilizing pre-trained weights, the learned features can be transferred to new tasks.</p>

<p style="text-align: justify;">Lightweight: The significance of the convolution filter within the network is assessed through the utilization of an optimized Taylor series expansion. This evaluation comprehensively takes into account the importance of both the convolution layer and the channel. During the network fine-tuning stage, an additional proposal is made to enhance model accuracy.</p>

<p style="text-align: justify;">Explainable: The integration of Grad-CAM and SHAP methods with adaptive weights is achieved by evaluating the uncertainty entropy value of the model's prediction.</p> 

<p style="text-align: justify;">We conducted extensive testing utilizing a database encompassing multiple types of aircraft. Nine advanced menthods were employed in our experiments, including models that take a one-dimensional tensor as input and optimized neural network models that adopt two-dimensional matrix as input. The final test results demonstrate that the proposed method in this paper achieves the highest accuracy.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-3.png" width="600">}}

## SDI

<p style="text-align: justify;">Next, I will introduce the details of the model calculation in detail, starting with the sensor data image. Illustrative plot for the fault injection is shown in Figure 2. The black lines denote clean states from real flight/simulation, and red lines are the fault-injected data. The FDC scheme is expected to detect the different fault cases based on available data and NN operations.</p>

<p style="text-align: justify;">Illustrative plots of the SDI is shown in Figure 3. Left: flight records that are collected from real flights/simulations; Middle: the cropped and down-sampled data. Right: the matrix that are linearly normalized within the range of 0-1 along each row.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-4.png" width="600">}}

<p style="text-align: justify;">After we get the stacked SDI images, what we need to focus on is how to use the VGG16 model with pre-trained weights for classification. Before inputting the VGG16 model, we use data augmentation methods to expand the original image. Specifically, there are 7 methods. For detailed calculation details, please refer to Part A of Section 3 of the paper.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-5.png" width="600">}}

## Model pruning

<p style="text-align: justify;">We denote the detection net as VGG16-FDC, and visualize its internal operations in Figure 6. The FDC net is expected to extract the feature information. Many feature maps, however, are completely black, which indicates that the associated convolutional kernels are not activated. Although the VGG16 architecture is sufficient for the FDC problem, it may also be oversized.</p>

<p style="text-align: justify;">In the paper, we used channel pruning menthod. In this paper, we have made 2 improvements. The first is Multi-scale kernels importance assessment, and the second is Improved knowledge distillation model to improve model pruning accuracy.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-6.png" width="600">}}

<p style="text-align: justify;">Given the assumption that parameters enclosed within the DNN are independent, model pruning is defined as a constrained optimization problem. We simplified the problem using Taylor expansion and ultimately obtained the importance scores of kernels.</p>

<p style="text-align: justify;">However, it is crucial to acknowledge that the importance scores of a given channel can have varying impacts on the model across different convolutional layers. To tackle this challenge, we introduce a novel approach called hierarchical and dynamic pruning. This approach considers the evaluation of importance for both channels and convolutional layers, enabling a comprehensive assessment that facilitates the adaptation of the network structure to enhance robustness. Figure 8 showcases the process of computing the importance of convolutional layers.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-7.png" width="600">}}

<p style="text-align: justify;">To enhance the fine-tuning process, a distillation menthod is employed. This part is the probability vector of the teacher model. This part is the probability vector of the student model. The output is the Kullback-Leibler divergence between the two probability vectors.</p> 

<p style="text-align: justify;">However, existing methods often overlook the classification accuracy of the teacher network on clean samples. Therefore, this paper introduces a threshold for knowledge distillation.</p>

<p style="text-align: justify;">This part is the Cross entropy Loss function. This part is used to control the proportion of loss items. When the prediction accuracy of the teacher model exceeds 90%, the value will be activated, otherwise the value will be 0.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-8.png" width="600">}}

<p style="text-align: justify;">Comparative experiments were conducted on distillation menthods with thresholds, and the results are illustrated in the figure 10. Through comparison, it is evident that incorporating a threshold in the distillation menthod significantly enhances the model's accuracy.</p>

<p style="text-align: justify;">For comparative purposes, aside from the channel pruning menthod that we eventually proposed, we also discuss Random, L1 unstructured, and L2 structured pruning methods in Table I. The model pruning method we proposed yields an aerospace sensors FDC net at 4.58 MB size and 98.99% accuracy.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-9.png" width="600">}}

## Explainability

<p style="text-align: justify;">As the FDC problem is transformed as abnormal regions detection on the augmented image, the core of explainability analysis thus lies in the visualization of the convolutional operations. For such purpose we adopt Grad-CAM, as shown in Figure 12.</p>

<p style="text-align: justify;">SHAP analysis has gained widespread adoption amongst researchers who conduct global explainability analysis. In this model, each prediction sample generates a prediction value, and the shapley value corresponds to the contribution assigned to each feature in the sample.</p>

<p style="text-align: justify;">Grad-CAM and SHAP are commonly used methods for neural network interpretability analysis. This paper aims to combine the characteristics of SHAP values and Grad-CAM by proposing an adaptive weight fusion menthod, which achieves a more comprehensive model interpretability.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-10.png" width="600">}}

<p style="text-align: justify;">Using the stacked image as input, Grad-CAM results from different convolutional layers of the pruned-VGG16-FDC neural network are plotted in Figure 14. In the first several layers of the net (1st and 2nd), the convolution focuses on extracting low-level features such as edges/lines on the image. As the convolutional layers stack deeper, more features appear in the 6th and 10th layers. As shown in Figure, the hotter areas in the 6th and 10th layers correspond to the protruding strip shown on the net input. In the last convolutional layer of the neural network, the hotter area focuses exclusively around the fault region.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-11.png" width="600">}}

<p style="text-align: justify;">When the uncertainty of Grad-CAM's predictions is around 0.45, as shown in Figure 15, the Grad-CAM-based interpretability method marks multiple channel. It is not possible to determine the faulty channel based on Grad-CAM. Similarly, the SHAP-based interpretation also marks the two channels ((1), (2)) as important. However, by analyzing the color intensity and length of the SHAP marks, as well as the distribution of Grad-CAM marks, it can be observed that both menthods have significant marks in the region (1) of the image. Therefore, it can be inferred that the third channel is faulty.</p>

<p style="text-align: justify;">In the case of Figure 16, where the uncertainty of Grad-CAM results is 0.15, the faulty channels are `p,q,r`. However, Grad-CAM menthod focuses on channels `\psi`, `\theta`, `\phi`. Based on the proposed fusion menthod, the faulty region can be more accurately marked.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-12.png" width="600">}}

## Comparison

<p style="text-align: justify;">We demonstrate the training process of the proposed menthod using the t-SNE method and ROC curve, demonstrating its superior performance.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-13.png" width="600">}}

<p style="text-align: justify;">Dividing the images into a 9:1 (training:testing) ratio, the pruned-VGG16-FDC is tuned using training data. To verify the effectiveness of the menthod proposed in this paper, we conducted a comparison with nine state-of-the-art fault detection menthods. The proposed model presented in this paper attains superior performance, achieving the highest accuracy while also considering the menthod's runtime. This consideration enhances the model's application performance, making it more suitable for practical implementation.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-14.png" width="600">}}

## Summary

<p style="text-align: justify;">An augmented imagefication based fault detection and classification (FDC) scheme for aerospace sensors are proposed. Measurement data of the sensors are first stacked into a image, data augmentation is then studied to inflate the stacked image to the size that is compatible with images commonly adopted in the machine vision realm. The FDC net is constructed via fine-tuning VGG16, a classical neural network, which is further pruned to compress the net size. Via extensive tests on a database, the proposed net yields 98.99% FDC accuracy across 4 aircraft at 5 flight conditions. Both Grad-CAM and fusion of Grad-CAM and SHAP analysis are also adopted in the paper, which justified both local and global operations enclosed within the net. The results of comparative experiments with nine advanced menthods in this paper demonstrate the effectiveness of the proposed FDC scheme.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-15.png" width="600">}}

<p style="text-align: justify;">This work has been accepted by the journal [IEEE TAES](https://ieeexplore.ieee.org/xpl/RecentIssue.jsp?punumber=7) and published online.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231220-16.png" width="600">}}







