# GANMM小结
### 简介
GANMM (Generative Adversarial Network Mixture Model) 是利用 GAN 进行聚类的一个模型，在传统聚类中非常典型的一个方法是利用高斯混合模型去匹配各个分布，高斯混合模型顾名思义就是利用多个高斯分布分别去拟合不同的分类，即有几个高斯分布就有几个聚类。但是高斯混合模型的最大缺点是无法拟合非常复杂的数据，在现实生活中大多数的数据并不符合高斯分布。

GAN是当下无监督学习中非常热门的一个模型，其在捕捉复杂数据分布上展现了非常好的效果。
基于以上原因 GANMM 便产生了。
##### 贡献
1. 设计出一个高效的聚类网络结构
2. 使用ϵ-Expectation-Maximization 算法防止 EM 在 GAN 中过早收敛的问题
3. 设计了一个防止聚类不平衡的算法
### 结构
如下图所示为GANMM的网络结构图：
![GANMM](https://img-blog.csdnimg.cn/20190609163118406.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### ϵ-EM
在GAN中如果一开始模型训练的太好可能导致EM算法过早收敛，为了解决这个问题可以让模型不要拟合的太好，论文中的方法为在 e-step 过程中引入一些错误项。
![GANMM](https://img-blog.csdnimg.cn/20190609172837880.png)
EM算法中的损失函数为：
![GANMM](https://img-blog.csdnimg.cn/20190609173014663.png)
在EM算法的训练过程中为了让 q(Z) match p(Z|D, θ)，就需要最小化 KL divergence，最后 KL-divergence 达到最小为0，但是在GANMM中我们规定 KL-dicergence 必须大于0，即两个分布并不能很好地匹配，这样就达到了让模型不要拟合的太好的目的。
由此我们可以得出损失函数为：![GANMM](https://img-blog.csdnimg.cn/20190609173621505.png)，由 ϵ > 0 可得：![GANMM](https://img-blog.csdnimg.cn/20190609173805934.png)
最后可得出递推不等式（具体推导请查看paper.）：
![GANMM](https://img-blog.csdnimg.cn/20190609173918624.png)
由上述不等式可以看出 ϵ 将会带来非常大的损失，但是如果能够保证![GANMM](https://img-blog.csdnimg.cn/2019060917422263.png)即 ϵi 的和是有上限的。为了使得 ϵ 有上限，作者将 cluster distribution 限制在一个有界空间 H 中，使得![GANMM](https://img-blog.csdnimg.cn/20190609174947940.png)，只要∀t : H t ⊆ H t−1 and |H t | > 0，那么就可以保证![GANMM](https://img-blog.csdnimg.cn/2019060917422263.png)

##### 聚类不平衡问题
初始化：在EM中一开始都是都是随机生成参数 θ，但是在 GANMM 中如果使用随即生成的参数 θ 进行训练极有可能造成所有的训练数据都被分配到同一个聚类。
为了解决这个问题在初始化时将训练数据平均划分到 N 个 Cluster 中，并分别训练GAN模型，之后再开始 ϵ-EM 算法。
同时在之后的训练中允许各自的 GAN 添加更多的数据，这些数据来自于其他聚类当中并且这些数据是被 classifier 分类为最有可能为当前聚类的数据。

如下图所示为GANMM具体演算法：
![GANMM](https://img-blog.csdnimg.cn/20190609163752474.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 损失函数
GANMM中使用的是WGAN，损失函数为：
![GANMM](https://img-blog.csdnimg.cn/2019060916434195.png)
