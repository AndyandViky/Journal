# ClusterGAN知识小结
- - -
## 简介
ClusterGAN 是第一个解决GAN在潜在空间聚类问题的工作，通过从一个one-hot编码变量和连续潜在变量的混合中对潜在变量进行采样，结合反向网络（将数据投射到潜在空间）与聚类特定损失共同训练，能够实现在潜在空间的聚类。
#### 主要贡献
在一般的GAN中，即使潜在变量保留了关于观测数据的信息，潜在点也会基于潜在分布而平滑地分散，导致没有可观察到的聚类。
为了解决上述问题，提出了三种主要的算法思想：
1.  利用离散和连续潜在变量的混合，以在潜在空间中创建非光滑几何
2.  提出了一种适应离散连续混合的新型反向传播算法，以及一个显式反向映射网络，以获得给定数据点的潜变量，因为该问题是非凸的。
3.  建议联合训练GAN以及具有聚类特定损失的反向映射网络，以便投影空间中的距离几何反映变量的距离几何。

如下图所示为使用离散连续采样进行训练的结果。
![GAN](https://img-blog.csdnimg.cn/2019060210142256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
## 结构
如下图所示为 ClusterGAN 的网络结构图，由 Generator，Discriminator，Encoder 组成，以下简称为 G，D，E，引入 E 的原因在于需要强制执行潜在向量的精确恢复：
训练流程为：从离散连续分布中采样，经过反向解码得出潜在变量，经由G生成fake-image，将fake-image分别丢入D和E，在D中的操作和普通GAN一样，在E中将fake-image反向解码为 Zn 和 Zc，希望经由 E 得出的结果应该要和输入一样。
![GAN](https://img-blog.csdnimg.cn/20190602101552806.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
#### 离散连续混合采样
ClusterGAN 在一个 one-hot vector 和正太随机变量串联组成的先验中采样，这样做的目的在于使得GAN能够做到在类间插值，即同时做到良好的插值和聚类，因为在传统上，潜在空间数据的连续性是良好插值的先行条件，如果潜在空间是连续的，那么便无法很好地实现聚类。换句话说，插值似乎与聚类目标存在分歧。
#### 反向传播解码
ClusterGAN 使用基于改进的反向传播解码，目的在于将 sample 到的样本转换为潜在向量（即可以作为 G 输入的向量）。
具体算法思想如下：
![GAN](https://img-blog.csdnimg.cn/20190602190729968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
#### 线性生成聚类
以下引理表明，对于离散连续混合，只需要在生成的空间中以线性生成来生成高斯混合。
引理：仅使用Zn进行聚类无法在线性生成的空间中恢复高斯数据的混合。 使用一个线性的G（·）将离散连续混合映射到高斯混合。
证明：如果潜在空间只是连续的部分，那么通过线性属性，任何线性生成只能在生成的空间中产生高斯。
G（·）映射离散连续混合到生成数据![在这里插入图片描述](https://img-blog.csdnimg.cn/20190602191700838.png)其中 w = {0,1,2...k} 是可能的，如果![GAN](https://img-blog.csdnimg.cn/20190602192059739.png)
A 为对角矩阵，其中的元素为 μi ( 1<= i <=k )。
## 损失函数
ClusterGAN 的损失函数在普通 GAN 的基础损失函数上增加了聚类特定损失项。
公式如下：
![GAN](https://img-blog.csdnimg.cn/2019060219373536.png)
其中正则化系数 βn 和 βc 的相对大小使得能够灵活地选择以改变保留潜在编码的离散和连续部分的重要性；![GAN](https://img-blog.csdnimg.cn/20190602194013145.png)被映射为接近相应簇的质心；![GAN](https://img-blog.csdnimg.cn/20190602194121560.png)为交叉熵损失，
类似作为K-Means的精神，该方法中的GAN训练涉及联合更新 θG 和 θE。
其联合更新算法如下：
![GAN](https://img-blog.csdnimg.cn/20190602194351949.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__Reference__:
https://blog.csdn.net/shaodongheng/article/details/83090137