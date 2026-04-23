+++
title = "Lec2线性分类器"
description = "Lec2线性分类器 - Lens Note"
date = "2026-03-03"
draft = false
tags = ["note"]
categories = ["笔记"]
+++

对于线性分类器，首先要有kNN分类。在训练上零成本，但是预测有成本
本质是划分线性空间，有几种方法：F1 manhatten distance  F2 distance
F1计算量小得多，但是对于所选取的特征要求很高
F2计算量大，但是不受特征选取的影响

与此同时，也有hyperparameter（超参数）
他主要在knn里面是对k的选取，超参数主要要人类手工调

与此同时，也讲了训练的方式，比如单一训练集验证（bad容易过拟合，泛化不行）
训练集与测试集，bad（容易过拟合，是在测试集上进行调参） 
训练集、测试集、validation集验证集（一般这样）
cross-validation训练（神经网络、cv比较少这样做）

# y=Wx+r  ——softmax classifier
我们以Softmax分类器+交叉熵损失为背景
1. 模型的原始输出（logits）：$z_{ik} = W_k^T x_i$（第 $i$ 个样本对应第 $k$ 类的原始得分，无激活）
2. Softmax激活（将logits转为概率）：$p_{ik} = P(Y=k|X=x_i) = \frac{e^{z_{ik}}}{\sum_{c=1}^C e^{z_{ic}}}$（$C$ 是总类别数）
3. 单个样本的交叉熵损失：$L_i = -\sum_{k=1}^C 1(y_i=k) \log(p_{ik})$（因为真实标签只有1个，实际可简化为 $L_i = -\log(p_{i,y_i})$） 
步推导偏导 
我们的目标是求 $\frac{\partial L_i}{\partial W_k}$，核心是链式法则：$\frac{\partial L_i}{\partial W_k} = \frac{\partial L_i}{\partial p_{ik}} \cdot \frac{\partial p_{ik}}{\partial z_{ik}} \cdot \frac{\partial z_{ik}}{\partial W_k}$ 
步骤1：求 $\frac{\partial L_i}{\partial p_{ik}}$ 交叉熵损失 $L_i = -\sum_{c=1}^C 1(y_i=c) \log(p_{ic})$，对 $p_{ik}$ 求偏导： $$\frac{\partial L_i}{\partial p_{ik}} = -\frac{1(y_i=k)}{p_{ik}}$$ （只有当 $c=k$ 时，求和项里的项才有非零导数，其余项导数为0）
步骤2：求 $\frac{\partial p_{ik}}{\partial z_{ik}}$（Softmax的导数） Softmax的导数是核心，先写Softmax公式：$p_{ik} = \frac{e^{z_{ik}}}{S}$，其中 $S = \sum_{c=1}^C e^{z_{ic}}$。 用商的导数法则：$\frac{\partial p_{ik}}{\partial z_{ik}} = \frac{e^{z_{ik}} \cdot S - e^{z_{ik}} \cdot e^{z_{ik}}}{S^2} = \frac{e^{z_{ik}}}{S} \cdot \frac{S - e^{z_{ik}}}{S} = p_{ik}(1 - p_{ik})$ （补充：如果求对 $z_{ic}(c≠k)$ 的导数，结果是 $-p_{ik}p_{ic}$，但这里我们只需要对 $z_{ik}$ 的导数） 
步骤3：求 $\frac{\partial z_{ik}}{\partial W_k}$ 因为 $z_{ik} = W_k^T x_i = \sum_j W_{kj} x_{ij}$（$j$ 是特征维度），对 $W_k$ 求偏导： $$\frac{\partial z_{ik}}{\partial W_k} = x_i$$ （每个权重分量 $W_{kj}$ 对 $z_{ik}$ 的偏导是 $x_{ij}$，整体向量形式就是 $x_i$）
步骤4：链式法则合并 把三步结果相乘： $$\frac{\partial L_i}{\partial W_k} = \left(-\frac{1(y_i=k)}{p_{ik}}\right) \cdot \left(p_{ik}(1 - p_{ik})\right) \cdot x_i$$

另一种解释方法
Kullback–Leibler divergence
![{\displaystyle D_{\text{KL}}(P\parallel Q)=\sum _{x\in {\mathcal {X}}}P(x)\,\log {\frac {P(x)}{Q(x)}}{\text{.}}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/c439efc63190e97cd68a1af930914ca0495a1702)
![[Pasted image 20260303201801.png]]
由此可知loss函数是啥
我们获取了loss函数，我们使用梯度下降的方式调整hyperparameter，进行调参

