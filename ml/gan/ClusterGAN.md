# ClusterGAN知识小结
- - -
## 简介
ClusterGAN 是第一个解决GAN在潜在空间聚类问题的工作，通过从一个one-hot编码变量和连续潜在变量的混合中对潜在变量进行采样，结合反向网络（将数据投射到潜在空间）与聚类特定损失共同训练，能够实现在潜在空间的聚类。
#### 主要贡献
在一般的GAN中，即使潜在变量保留了关于观测数据的信息，潜在点也会基于潜在分布而平滑地分散，导致没有可观察到的聚类。
为了解决上述问题，提出了三种主要的算法思想：
1.  我们利用离散和连续潜在变量的混合，以在潜在空间中创建非光滑几何
2.  我们提出了一种适应离散连续混合的新型反向传播算法，以及一个显式反向映射网络，以获得给定数据点的潜变量，因为该问题是非凸的。
3.  我们建议联合训练GAN以及具有聚类特定损失的反向映射网络，以便投影空间中的距离几何反映变量的距离几何。

如下图所示为使用离散连续采样进行训练的结果。
![GAN](https://img-blog.csdnimg.cn/2019060210142256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
## 结构
如下图所示为 ClusterGAN 的网络结构图，由 Generator，Discriminator，Encoder 组成，以下简称为 G，D，E，引入 E 的原因在于需要强制执行潜在向量的精确恢复：
训练流程为：从离散连续分布中采样，经过反向解码得出潜在变量，经由G生成fake-image，将fake-image分别丢入D和E，在D中的操作和普通GAN一样，在E中将fake-image反向解码为 Zn 和 Zc，我们希望经由 E 得出的结果应该要和输入一样。
![GAN](https://img-blog.csdnimg.cn/20190602101552806.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
#### 离散连续混合采样
#### 反向传播解码
#### 线性生成聚类
#### 数据中不同类别的分割模式
#### 潜在空间中的插值
## 损失函数
__Reference__:
https://blog.csdn.net/shaodongheng/article/details/83090137