# BEGAN知识小结
- - -
###### 本文为阅读完BEGAN论文后作出的系统小结。[论文地址](https://arxiv.org/abs/1703.10717 "论文地址")
### 简介
BEGAN是Google在17年上半年出的一篇论文，该论文对GAN做了进一步的改进，使得在没有使用一些trick的时候GAN依旧可以训练的很好，模式奔溃和训练不平衡等问题也解决的非常好。
以往的GAN都是希望生成器生成的model和真实数据的model分布尽量相近，BEGAN从loss的角度进行比较，希望生成器生成的model的loss和真实数据的model的loss分布尽量相近，由于使用的是同一个encoder和decoder去解码和编码，Autoencoder本身就会提取图像的共性，比如人脸的轮廓信息，那么当两者loss分布逼近的时候，两者model的分布也将会逼近。

BEGAN中，作者做出了以下四个贡献： 
1. 提出了一种新的简单强大GAN网络结构，使用标准的训练方式不加训练trick也能很快且稳定的收敛。
2. 对于GAN中G，D的能力的平衡提出了一种均衡的概念。
3. 提供了一个超参数，这个超参数可以在图像的多样性和生成质量之间做均衡。
4. 提出了一种收敛程度的估计，这个机制只在WGAN中出现过。

### 结构，损失函数
BEGAN采用autoencoder结构的discriminator。
下图为BEGAN整体结构图。
![结构](https://pic1.zhimg.com/80/v2-ad7bed2e916c7d55e6180efcfd3271f4_hd.jpg)
其中损失函数使用的是 L1，L2 norm。
![loss](https://www.zhihu.com/equation?tex=%5Cmathcal%7BL%7D%28v%29=%7B%5C%7Cv%20-%20D%28v%29%5C%7C%7D_p%20%5C%20,%20%5Cquad%20p%20%5Cin%20%7B1,2%7D)
作者在论文中表示：在大量实验下发现每个pixel的重构误差实际上是独立同分布的，并且都是（近似）正态分布。根据中心极限定理，整个图像的重构误差也将服从相同的正态分布。
给定两个正太分布![在这里插入图片描述](https://www.zhihu.com/equation?tex=%5Cmu_1%20=%20%5Cmathcal%7BN%7D%28m_1,%20c_1%29,%20%5Cquad%20%5Cmu_2%20=%20%5Cmathcal%7BN%7D%28m_2,%20c_2%29)，可以算出这两个分布之前的Wasserstein distance：
![](https://www.zhihu.com/equation?tex=W%28%5Cmu_1,%20%5Cmu_2%29%5E2%20=%20%28m_1%20-%20m_2%29%5E2%20%2b%20%28c_1%20%2b%20c_2%20-%202%5Csqrt%7Bc_1%20c_2%7D%29)
作者提出假设![在这里插入图片描述](https://www.zhihu.com/equation?tex=%5Cfrac%7Bc_1%20%2b%20c_2%20-%202%5Csqrt%7Bc_1%20c_2%7D%7D%7B%28m_1%20-%20m_2%29%5E2%20%7D)是一个常数或者是关于W的递增函数。
那么基于该假设，我们可以通过最大化![在这里插入图片描述](https://www.zhihu.com/equation?tex=%28m_1-m_2%29%5E2)来最大化![在这里插入图片描述](https://www.zhihu.com/equation?tex=W%28%5Cmu_1,%20%5Cmu_2%29%5E2)，根据GAN的对抗原则，D需要不断拉大W的距离即最大化![在这里插入图片描述](https://www.zhihu.com/equation?tex=W%28%5Cmu_1,%20%5Cmu_2%29)，而G则是要拉近距离。

那么最大化（m1 - m2）其实有两组解。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190517110916463.png)
在论文中作者选择的是第二组解，因为最小化么m1，最大化m2不仅可以拉大两个分布之间的距离同时也可以减小真实图片的重构误差，那么G在拉近两个分布之间的距离时最小化m2即可。

__损失函数__：如下图所示为BEGAN的Loss function。
![](https://img-blog.csdnimg.cn/20190517130230638.png)
在GAN的训练中非常容易出现训练不平衡的现象，即 L 和 D 训练的进度不一样导致一方的能力大于另外一方。为解决这个问题，作者提出了一个超参数![在这里插入图片描述](https://www.zhihu.com/equation?tex=%5Cgamma%20%5Cin%20%5B0,1%5D)来平衡双方的loss。其中：
![](https://img-blog.csdnimg.cn/20190517131509776.png)
当超参数较小时，D致力于最小化真实样本的重构误差，相对来说，而对生成样本的关注较少，这将导致生成样本的多样性降低。作者称这个超参数为diversity ratio，它控制生成样本的多样性。
为了尽可能地满足公式二，作者借鉴控制论中的“比例控制理论”(Proportional Control Theory)，引入比例增益![在这里插入图片描述](https://www.zhihu.com/equation?tex=%5Clambda_k)和比例控制器的输出![k_t](https://www.zhihu.com/equation?tex=k_t)。
完整的损失函数公式如下：
![](https://img-blog.csdnimg.cn/20190517133008684.png)
综上所述，我们可以列出一个判断收敛的指标。从以下公式中可以看出，M越小表示训练的越好。
![在这里插入图片描述](https://www.zhihu.com/equation?tex=%5Cmathcal%7BM%7D%20=%20%5Cmathcal%7BL%7D%28x%29%20%2b%20%7C%5Cgamma%20%5Cmathcal%7BL%7D%28x%29%20-%20%5Cmathcal%7BL%7D%28G%28z%29%29%7C)
BEGAN网络结构图如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019051713335096.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

__Reference:__
https://arxiv.org/abs/1703.10717
https://zhuanlan.zhihu.com/p/26394806
https://blog.csdn.net/qq_25737169/article/details/77575617