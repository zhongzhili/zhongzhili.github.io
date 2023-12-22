---
title: Battery thermal runaway prediction based on integrated algorithm
date: 2023-12-22
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

{{<end>}}

## Background

<p style="text-align: justify;">Battery thermal runaway means that the battery generates excessive heat during charging or discharging, causing a sharp rise in temperature, and may even cause an explosion or fire. Therefore, in order to ensure the safety and stability of batteries, big data prediction of battery thermal runaway technology has become crucial. In order to effectively prevent and respond to battery thermal runaway risks, researchers have developed a variety of battery thermal runaway detection technologies. These solutions and methods can be divided into three categories: model-based, signal-based and knowledge-based. Existing fault diagnosis methods have certain limitations, such as difficulty in identifying model parameters, lack of reliable and online-applicable signal processing and feature extraction methods, poor stability and low robustness of single model detection, etc. Furthermore, it is difficult to quickly detect faults in their early stages based on actual operating data.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231222-1.png" width="600">}}

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231222-2.png" width="600">}}

## Research introduction

<p style="text-align: justify;">In order to improve the accuracy and reliability of battery thermal runaway detection, we apply stack learning to this field. Existing battery thermal runaway detection methods usually use a single detection model. Due to the complexity of the battery system, a single model is difficult to fully capture the battery. Diverse characteristics of thermal runaway. Stacked learning integrates multiple different types of detection models, such as support vector machine (SVM), random forest (Random Forest), neural network, etc., to combine the advantages of each model and improve detection accuracy and generalization capabilities. In addition, we designed 11 statistical features to characterize the rich information of the original data. At the same time, the stacked learner has strong automatic learning and feature selection capabilities, and can automatically extract the most informative features from the original data and optimize the detection performance.</p>

## Feature engineering

<p style="text-align: justify;">The 11 statistics and their calculation formulas:</p>
1.Absolute Mean

`$$\bar{x}=\frac{1}{N} \sum_{i=1}^{N}\left|x_{i}\right|$$`

2.Standart Deviation

`$$\sigma=\sqrt{\frac{1}{N} \sum_{i=1}^{N}\left(x_{i}-\bar{x}\right)^{2}}$$`

3.Skewness

`$$\mathrm{Sk}=\frac{1}{N} \sum_{i=1}^{N} \frac{\left(x_{i}-\bar{x}\right)^{3}}{\sigma^{3}}$$`

4.Kurtosis

`$$\mathrm{K}=\frac{1}{N} \sum_{i=1}^{N} \frac{\left(x_{i}-\bar{x}\right)^{4}}{\sigma^{4}}$$`

5.Entropy

`$$H(X)=-\sum_{i=1}^{N} P\left(x_{i}\right) \log P\left(x_{i}\right)$$`

6.RMS

`$$x_{r m s}=\sqrt{\left(\frac{1}{N}\right) \sum_{i=1}^{N}(x)^{2}}$$`

7.Peak to Peak

`$$x_{p}=\max \text { value }-\min \text { value }$$`

8.Crest Factor

`$$x_{c r e s t}=\frac{\text { max value }}{\mathrm{x}_{\mathrm{rms}}}$$`

9.Clearence Factor

`$$x_{\text {clear }}=\frac{x_{p}}{\left(\frac{1}{N} \sum_{i=1}^{N} \sqrt{\left|x_{i}\right|}\right)^{2}}$$`

10.Shape Factor

`$$\frac{\mathcal{X}_{r m s}}{{\bar{x}}}$$`

11.Impulse

`$$\frac{\text { max value }}{\bar{x}}$$`

## Integrated learning algorithm based on Stacking

<p style="text-align: justify;">The Stacking integrated learning framework first divides the original data set into several sub-datasets and inputs them into each base learner of the first-layer prediction model. Each base learner outputs its own prediction result. Then, the output of the first layer is used as the input of the second layer to train the meta-learner of the second layer prediction model, and then the model in the second layer outputs the final prediction result. The Stacking learning framework generalizes the output results of multiple models to improve the overall prediction accuracy.</p>

## Battery thermal runaway prediction model based on multi-model fusion Stacking ensemble learning method

<p style="text-align: justify;">Based on the prediction ability of the base learner, in the first layer of the Stacking model, in addition to the XGBoost algorithm that is widely used in major competitions, we also selected the random forest (RF) with excellent prediction performance. As a base learner, the model adopts the bagging integrated learning method, has excellent learning ability and rigorous mathematical theory support, and has been widely used in various fields. To sum up, the first layer of the stacking integration model initially selects the base learners as XGBoost and RF, and the second layer selects the K nearest neighbor model as the meta-learner. The model architecture is shown in the figure below.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231222-3.png" width="650">}}

<p style="text-align: justify;">In addition, the training set of the meta-learner is generated from the output of the base learner. If the training set of the base learner is directly used to generate the secondary training set, serious overfitting may occur. In order to prevent the data from being repeatedly learned by the double-layer learner and avoid the occurrence of the "over-fitting" effect, the data usage process needs to be divided reasonably. According to the two selected base learners, the original training data set needs to be divided into two sub-data sets first, and the data IDs of each piece of data do not overlap each other. For a single base learner, use one data block as the training set, corresponding to The remaining data blocks serve as the test set. Each base learner can output a prediction result for its own test data set, and the two results can eventually be merged into a new data set.</p>

## Test Results

<p style="text-align: justify;">We choose the XGBoost model and the random forest model as comparison models to verify the effect of the stacked model. First, the XGBoost model and the random forest model were used as thermal runaway fault detection algorithms to conduct experiments, and the results are shown in the figures. It can be found that XGBoost and random forest algorithms still have the problem of classification errors for some data. In particular, both of them misclassify a large amount of thermal runaway data as normal data in real situations. The effect of the model still has a lot of room for improvement. We use the XGBoost model and the random forest model as the first layer classifier of the stacked model, and the KNN model as the second layer classifier. The results of training and testing using the same data are shown in the figure. It can be found that the model proposed in this patent effectively improves the problem of low thermal runaway detection accuracy. At the same time, analyzing the results shown in the table, the stacking model proposed in this patent not only improves the accuracy of model testing, but also improves the accuracy of the training process. And the deviation of the accuracy results of multiple trainings is reduced, which shows that this model can effectively improve the accuracy and stability of detection.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231222-4.png" width="600">}}

| Water | XGBoost | RF | Stacking Model |
|---------|---------|---------|---------|
| Train CV Accuracy | 0.88 (+/- 0.11) | 0.93 (+/- 0.03) | **0.96 (+/- 0.01)** |
| Train Accuracy | 0.75 | 0.94 | **0.98** |
| Test Accuracy | 0.72 | 0.92 | **0.96** |








