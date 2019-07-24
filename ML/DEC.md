# Deep Embedded Cluster
## 简介
一般的聚类算法例如K-means，GMM，这些方法速度快，适用于各种各样的问题， 但是，它们的距离度量仅限于原始数据空间，当输入维度较高时，它们往往无效。因此突出DEC聚类，DEC可以同时学习使用深度神经网络的特征表示和聚类分配，DEC从数据空间学习映射到低维特征空间，在该特征空间中迭代地优化聚类目标。

DEC定义了一个基于质心的概率分布，并将KL散度最小化为一个辅助目标分布，以同时改善聚类分配和特征表示，而不是最小化KL散度以产生忠实于原始数据空间中的距离的嵌入。 基于质心的方法还具有降低 O(nk) 的复杂度的优点，其中k是质心的数量。

DEC通过同时学习特征空间Z中的k个聚类中心 $\{u_{j} \in Z\}_{j=1}^k$ 和将数据点映射到Z的DNN的参数 $\theta$ 来聚类数据。DEC具有两个阶段:( 1）使用自动编码器进行参数初始化，(2）参数优化（即聚类），其中我们在计算辅助目标分布和最小化Kullback-Leibler（KL）散度之间进行迭代。

__贡献：__
（a）深度嵌入和聚类的联合优化; 
（b）通过软分配进行新颖的迭代改进; 
（c）得出聚类精度和速度方面最先进的聚类结果；

## KL散度
给定非线性映射 $f_{\theta}$ 和初始聚类质心 $\{u_{j}\}_{j=1}^{k}$ 的初始估计，DEC使用在两个步骤之间交替的无监督算法来改进聚类。 在第一步中，我们计算嵌入点和集群质心之间的软分配。 在第二步中，我们更新深度映射 $f_{\theta}$ 并通过使用辅助目标分布从当前高置信度分配中学习来优化聚类质心。 重复这个过程直到满足收敛条件。

## 软分配
基于 t-sne 的思想，DEC使用 t 分布作为核来测量嵌入点和质心之间的相似度。
$$q_{ij}=\frac{(1+||z_{i}-u_{j}||^2/\alpha)^\frac{\alpha+1}{2}}{\sum_{j^`}(1+||z_{i}-u_{j^`}||^2/\alpha)^\frac{\alpha+1}{2}}$$
其中 $z_{i}=f_{\theta}(x_{i}) \in Z$ 对应于嵌入后的 $x_{i} \in X$ ， $\alpha$ 是Student-t分布自由度， $q_{ij}$ 可解释为将样本i分配给聚类j的概率（即软分配）。在所有实验中作者设置 $\alpha$ = 1。

作者提出另一个辅助分布也用来衡量样本属于某个聚类的分布：
$$p_{ij}=\frac{q_{ij}^2/f_{j}}{\sum_{j^`}q_{ij^`}^2/f_{j^`}}$$
其中 $f_{j}=\sum_{i}q_{ij}$ 是软群频率。
作者的论文中说该公式主要考虑以下三点:
1. 强化预测。q分布为软分配的概率，那么p如果使用delta分布来表示，显得比较原始。
2. 置信度越高，属于某个聚类概率越大。
3. 规范每个质心的损失贡献，以防止大类扭曲隐藏的特征空间。分子中那个$f_{j}$就是做这个的。
## KL散度的测量
综上所述，作者给出了原始分布和假设分布，那么就需要使用 KL-divergence 去拉近两分布之间的距离：
$$L=KL(P||Q)=\sum_{i}\sum_{j}p_{ij}\log\frac{p_{ij}}{q_{ij}}$$

每次迭代更新需要Update的参数为：
$$\frac{\partial L}{\partial z_{i}}=\frac{\alpha+1}{\alpha}\sum_{j}(1+\frac{||z_{i}-u_{j}||^2}{\alpha})^{-1}\times(p_{ij}-q_{ij})(z_{i}-u_{j})$$
$$\frac{\partial L}{\partial u_{j}}=-\frac{\alpha+1}{\alpha}\sum_{i}(1+\frac{||z_{i}-u_{j}||^2}{\alpha})^{-1}\times(p_{ij}-q_{ij})(z_{i}-u_{j})$$
第一个公式是优化AE中的Encoder参数，第二个公式是优化聚类中心。也就是说作者同时优化了聚类和DNN的相关参数。
## 结构图
![DEC](https://img-blog.csdnimg.cn/20190630092748527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
DEC算法由两部分组成，第一部分会预训练一个AE模型；第二部分选取AE模型中的Encoder部分，加入聚类层，使用KL散度进行训练聚类。

References:
https://zhuanlan.zhihu.com/p/50365577
https://flashgene.com/archives/7798.html
https://zhuanlan.zhihu.com/p/28967965
http://proceedings.mlr.press/v48/xieb16.pdf