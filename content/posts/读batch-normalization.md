+++
title = "读batch normalization"
description = "读batch normalization - Lens Note"
date = "2026-05-09"
draft = false
categories = ["read paper"]
+++

batch normalization 源于朴素的思想：在反向传播的过程中，诸如sigmoid等等activation函数在||x||趋于无穷大的时候的导数趋于0，这导致我们在梯度下降的时候极其容易陷入梯度消失的状态，导致极其难以收敛
这里于是引入了covariate shift这个概念，即change in the distribution of the network parameters during training due to the change in network parameters during training 引入这个统计学概念，即通过改变输入来消除这个covariate shift 是我们的第一想法
在此前whitening操作一直存在，这里首先是第一个办法：modify the network directly. 但是这里存在很显然的问题就是:x'=x-E[x]  那么当我们做出梯度下降，给x加了一个偏置项，x''=x+b  那么x'=x''-E[x'']=x-E[x]那么梯度下降就会失效这很显然不正确。他本质陷入的问题是将norm和optimization割裂开来，这样会导致norm失效
下面第二种办法即为全局whitening：x'=Norm(x,X)其中x为单样本，X为训练集,这里x' = $Σ^{-1/2} (x - E[x])$,其中Σ为$Cov[x]=E[x x^T]-E[x]E[x]^T$   这个理论主要是由于最优化中的通过标准化和去相关化，将hessian矩阵标准化，把条件数κ调整为1，从而实现理论上的梯度最速下降，但是这样计算逆矩阵的代价太过expensive，因为矩阵的分解极其复杂，计算量过大，很容易就被放弃
第三种办法即为Lyu & Simoncelli 的除法归一化操作：$$y_i = \frac{x_i}{\left(\sum_{j \in N(i)} w_j x_j^2 + \sigma^2\right)^{-0.5}}$$
但是这样norm会造成每一个batch单独进行归一化，使得相对位置信息丢失，使特征信息大量丢失
而本文的batch normalization是采取了改进全局whitening的策略，采取了2个simplication，第一是不采用整个训练集进行估计，这样太过繁琐，而是将每一个batch的每一个维度的量进行E(x)与Var(x)的估计，这样可以极大减小计算量。  第二是采取每一个维度进行norm，这样保留了相对位置信息，不会陷入方法三丢失特征的问题，与此同时为了解决激活函数的输入不会总处于线性区，还引入了γ和β  ，作为y=γ*x'+β 进行计算，再输入进入activation ，与此同时这个batch normalization layer也作为一层嵌入 ，note: 在这里引入偏置β极为重要，因为他本质上解决了只用局部batch进行预估E[x]与Var[x]的loss对b的梯度为0的问题，引入这个偏置项解决了这个问题
下面即为batch normalization的全流程

![image](https://raw.githubusercontent.com/lens365/lens_website/main/static/images/image-1778339982647-2f1hg3.png)


与此同时，我们也可以从本论文中很明显感受到batch size对于normalization的影响很大，因此受制于显存等等影响，小batch size很必要，因此本方法仍具有一定的缺陷