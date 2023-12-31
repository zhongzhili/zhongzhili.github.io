---
title: Request prediction of virtual machine resources (references paper)
date: 2023-12-18
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

Article [link](https://tidel.mie.utoronto.ca/pubs/tracegen_camera_20210926.pdf) if you are interested.

{{<end>}}

## Article title

Generating Complex, Realistic Cloud Workloads using Recurrent Neural Networks

## Author and organization

Shane Bergsma (Huawei Canada Research Center), Timothy Zeyl (Huawei Canada Research Center), Arik Senderovich (University of Toronto), J. Christopher Beck (University of Toronto)

## Problems solved by the article

<p style="text-align: justify;">To predict the load of virtual machines dynamically created by users on the cloud, a Cloud Workload can be simply regarded as a Trace of VMs created by users. Each record records the creation time of the VM and the number of resources requested by the VM (CPU, Memory, Network, etc.), VM survival time, etc.</p>

<p style="text-align: justify;">If the algorithm can accurately predict how the workload will change in the future, resource allocation can be planned more accurately. The accurate prediction model can also be used to tune the scheduling algorithm, improve resource utilization, etc.</p>

## Part of the difficulty with this problem

<p style="text-align: justify;">One of the difficulties is that historical data cannot be used directly for prediction and optimization:</p>

<p style="text-align: justify;">The amount of historical data is not large enough. For example, a scheduler based on reinforcement learning requires large batches of data;</p>

<p style="text-align: justify;">Historical data has limitations. It contains limited information and cannot be used for long-term load forecasting;</p>

## Limitations of other approaches to the problem

<p style="text-align: justify;">Traditional modeling method:</p>

<p style="text-align: justify;">It is assumed that the time when a user creates a VM follows an independent Poisson distribution;</p>

<p style="text-align: justify;">It is assumed that user demand for resources follows an independent polynomial distribution;</p>

<p style="text-align: justify;">It is assumed that the life cycle of each VM (or Job) is also independent of each other.</p>

<p style="text-align: justify;">Various independence assumptions in traditional modeling methods make the fitting between modeling results and real data low, making it difficult to generate real, high-quality workloads, resulting in inaccurate final decisions.</p>

## The idea of the method proposed in the article

<p style="text-align: justify;">This paper introduces a predictive model for large-scale cloud workloads that captures long-distance inter-job correlations in terms of arrival rates, resource requirements, and lifetimes. The workload is modeled as a three-stage generation process with three separate models: (1) predicting the number of batch arrivals over time (Batch Arrival Modeling), (2) predicting the order in which resources are requested (Resource Modeling), and (3) predicting life cycle (Lifetime Modeling). Figure 1 shows the calculation framework of the algorithm. It takes historical data as input and passes through three models. Each model uses the output of the previous model as the input of the current model. Finally, a Trace is generated, which contains the creation of each VM. time, end time, and request for resources.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-1.png" title="Figure 1. Three-stage workload generation process in a cycle" width="260">}}

Next, the three stages will be analyzed in turn:

**Batch Arrival Modeling**

<p style="text-align: justify;">The first model uses Poisson regression to generate the number of batches arriving within a period of time (e.g. 5 min) (a batch is defined as all jobs submitted by the same user within this period of time).</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-2.png" width="500">}}

Poisson regression model establishment:

<p style="text-align: justify;">Non-homogeneous Poisson regression assumes that the number of events `N_p` occurring in each time period is modeled by a Poisson distribution with a mean of `\mu_{p}`, and its expression is `\mu_{p}=\exp (w \cdot x_{p})`, where `x_p` is a feature vector and `w` is a parameter to be optimized vector. Given some training data consisting of an event number vector `y` and a feature vector matrix `X`, fit the parameters by minimizing the negative log-likelihood (NLL) of the training data:</p>
$$
\begin{aligned}
\text{Loss} & =-\log (\operatorname{Pr}(\mathbf{y} \mid \mathbf{X}, \mathbf{w})) \\
& =\sum_{p} \mu_{p}-y_{p} \log \left(\mu_{p}\right)
\end{aligned}
$$

<p style="text-align: justify;">The article adds an elastic network regularization term to this loss function to constrain parameters, reduce model complexity, and reduce the risk of overfitting of the model.</p>

<p style="text-align: justify;">The feature vector matrix X contains three types of time feature information:</p>

<p style="text-align: justify;">HOD: Hour-of-day, from 1 to 24 (one-hot-encoded);</p>

<p style="text-align: justify;">DOW: Day-of-week, from 1 to 7 (one-hot-encoded);</p>

<p style="text-align: justify;">DOH: Day-of-history, from 1 to `N` (survival-encoded), where there are `N` total days in the history;</p>

**Resource Modeling**

<p style="text-align: justify;">The second model uses LSTM to generate flavors (i.e. requested resource types, such as CPU, Memory) for all jobs within a period of time.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-3.png" width="500">}}

<p style="text-align: justify;">At each calculation step `t`, the Flavor LSTM returns a distribution containing `K` (`K` is 16) possible flavors, and a special end-of-batch (EOB) token to indicate a user's set of job requests over a period of time. The LSTM generates vectors of length `K+1`, which are fed into the softmax function, which returns in step `t`, the multinomial distribution over the `K` species label to which the flavor belongs:</p>

`$$\operatorname{Pr}\left(\hat{f}_t=k \mid \mathbf{y}_t\right)=\frac{\exp \left(y_t^k\right)}{\sum_{k^{\prime}=1}^{K+1} \exp \left(y_t^{k^{\prime}}\right)}$$`

<p style="text-align: justify;">The standard method of minimizing the negative log-likelihood of training data (CrossEntropyLoss) is used to optimize the adjustment parameters. The loss value of the article is the cross-entropy loss value plus the value of the regularization penalty term, and then the parameters are optimized through gradient backpropagation.</p>

<p style="text-align: justify;">The main flavor feature at step `t` is one-hot encoding of the flavor from the previous step `t-1`. If the previous step was the end of the batch, or the first flavor in the sequence is being generated, then pass the EOB token as input.</p>

**Lifetime Modeling**

<p style="text-align: justify;">The third model uses LSTM to generate the lifetime distribution of each job.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-4.png" width="500">}}

<p style="text-align: justify;">The hazard function is constructed, which is derived from the perspective of survival analysis. It refers to the event incidence rate at time `t` as of time `t` or greater than time `t`. In layman's terms, it is a derivative, represented by lambda or `h`.</p>

`$$h=\lim _{d t \rightarrow 0} \frac{\operatorname{Pr}(t \leq T<t+d t)}{d t \cdot S(t)}=\frac{f(t)}{S(t)}=-\frac{S^{\prime}(t)}{S(t)}$$`

<p style="text-align: justify;">In this article, the risk function value is calculated through LSTM. Specifically, the article uses 47 bins in the hazard function. Considering a given set of output `h(j), j= 1...J`, calculate the lifetime The probability of falling in bin `k`:</p>

`$$\operatorname{Pr}(\text { lifetime }=k)=h(k) \prod_{j=1}^{k-1}(1-h(j))$$`

<p style="text-align: justify;">where `h(k)` is the value obtained by mapping the output of LSTM through the logistic function:</p>

`$$h\left(j \mid \mathbf{x}_t\right)=\frac{1}{1+\exp \left(y_t^j\right)}$$`

<p style="text-align: justify;">Optimize the parameters of the network in a way that minimizes the negative log-likelihood of the training data.</p>

**Generated Workload**

<p style="text-align: justify;">Figure 2 shows the visualization results of the workload generated using the method proposed in this article, the workload generated using the traditional method, and the real workload. In each workload diagram, each row represents a fixed period of time (e.g. 5 minutes), each small square represents the application for a VM resource, and the small squares connected together represent that these applications come from the same user (a batch), different batches are separated by a gap. The length of the square represents the life cycle of each job, and the color of the square represents the resource type applied for by each job.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-5.png" title="Figure 2. Workload/model visualization: Each rectangle represents a VM, and each row shows all VMs that arrived within 5 minutes. The color represents the VM flavor and the width represents the life cycle. Each row describes the resources and lifecycle attributes of the virtual machine that arrive sequentially over a period of time. In real workloads, VMs arrive in user-specific batch format (here separated by spaces), the VMs in each batch have similar properties, and the arrival rate varies greatly across different time periods." width="500">}}

<p style="text-align: justify;">It can be found from this visualization that the workload generated by the method proposed in this article is more complex and closer to the real workload than the traditional method.</p>

## Verification of Article results

<p style="text-align: justify;">Test based on the open source code "https://github.com/huaweicloud/trace_generation_rnn".</p>

**Batch Arrival Modeling**

Input data and it's shape:

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-6.png" width="300">}}

y_train (5999,)

<p style="text-align: justify;">x_train (5999, 52), the 52-dimensional feature vector contains three time features (HOD, DOW, DOH).</p>

Test results based on Poisson regression model:

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-7.png" width="600">}}

The Poisson regression 90% coverage on the test set is 0.83.

**Resource Modeling**

Input data and it's shape:

y_train torch.Size([3036, 500])

<p style="text-align: justify;">x_train torch.Size([3036, 500, 1, 69]), the 69-dimensional feature vector contains three types of features (52-dimensional time feature, 16-dimensional category feature, and 1-dimensional token).</p>

Flavor LSTM model construction:

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-8.png" width="600">}}

Training results based on Flavor LSTM model (maximum iteration is 10 times):

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-9.png" width="300">}}

Test results based on Flavor LSTM model:

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-10.png" width="600">}}

Test results based on Flavor LSTM model:

**Lifetime Modeling**

Input data and it's shape:

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-11.png" width="600">}}

_train torch.Size([3036, 500, 47])

<p style="text-align: justify;">x_train torch.Size([3036, 500, 1, 175]), the 175-dimensional feature vector contains four features (time features (HOD, DOW, DOH), Requested resources of current job, Number of jobs in current batch, Lifetime of previous job).</p>

Training results based on Duration LSTM model (maximum iteration is 60 times):

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-12.png" width="300">}}

Test results based on Duration LSTM model:

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231218-13.png" width="600">}}

The BCE value is about 0.129, and the error rate is close to 29%.







