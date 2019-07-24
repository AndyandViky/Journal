# 高斯混合模型知识小结
---
## 简介
高斯混合模型(Gaussian Mixed Model)顾名思义，指的是多个高斯分布函数的线性组合，理论上GMM可以通过不同个数的高斯分布拟合出任意类型的分布。
设有随机变量X，则混合高斯模型可以用下式表示：
![GMM](https://img-blog.csdnimg.cn/20190601151037171.png)
其中πk是混合系数（mixture coefficient），满足：
![GMM](https://img-blog.csdnimg.cn/2019060115153460.png)
## GMM的应用
GMM一般用于聚类，有几个高斯分布函数即代表有几个分类，在GMM中选取一个点需要经过两个步骤：1.先首先随机地在这 K 个 Component 之中选一个，每个 Component 被选中的概率实际上就是它的系数πk，2.选中Component之后问题就回归到在单个高斯分布中sample一个点。
假设已知 K = 2， 那么高斯模型可写成：
![GMM](https://img-blog.csdnimg.cn/2019060115235611.png)
其中 π1，π2 为两个高斯分布对应的权重，现在主要的问题在于如何估计未知的参数，即在sample一个点后如何才能知道该点属于哪个高斯分布。
## GMM参数估计过程
为了方便引入了一个新的 k 维变量 z （z为一个one-hot vector），用来更加方便地表示选中某个分类的概率。其中 z 的取值范围为 0 或者 1，zk = 1 表示第 k 类被选中的概率，zk = 0 表示第 k 类未被选中的概率。
z 应该满足以下两个条件：
![GMM](https://img-blog.csdnimg.cn/20190601161643263.png)
zk = 1 的概率就是πk，假设 zk 之间是独立同分布的，那么 z 的联合概率分布为：
![GMM](https://img-blog.csdnimg.cn/20190601162141541.png)
p( x | zk=1 ) 为第 k 类的正太分布函数，可以表示为：
![GMM](https://img-blog.csdnimg.cn/20190601163026888.png)
上式可以改写为：
![GMM](https://img-blog.csdnimg.cn/20190601163132556.png)
由全概率公式可求出 p(x):
![GMM](https://img-blog.csdnimg.cn/20190601163246380.png)
由以上三个概率公式可求出第 k 个分量的后验概率，可以表示为：
以下为贝叶斯公式求出的后验概率，计算该后验概率的目的在于方便用于EM算法进行参数估计。
![GMM](https://img-blog.csdnimg.cn/20190601221700743.png)
## EM算法估计GMM参数
### EM算法简介
EM算法的核心作用在于估计已知分布的未知参数。
EM算法的步骤为：
1. 第一轮随机给出分布的参数
2. 根据参数计算出最大似然
3. 根据似然重新计算参数
4. 根据参数变动大小不断动态更新参数直到收敛

在一般情况下似然函数都会使用log方便计算，但是EM算法中的似然函数由于会出现"log和"的情况，在后续求导上会造成极大的困难，未解决这个问题需要使用 Jensen 不等式。
设X是一个随机变量，f(X)是一个凸函数（二阶导数大或等于0），那么有：
![GMM](https://img-blog.csdnimg.cn/20190601222713243.png)
当且仅当X是常数的时候等号成立，如果f(X) 是凹函数，不等号反向。

使用该不等式的意义在于计算似然困难的情况下，我们可以找到似然函数的一个紧的下界并且一直优化它，并保证每次迭代能够使总的似然函数一直增大。

如下图所示，当随机变量X为常数时可以使 Jensen 不等式等号成立，即存在点使得下图中的黑色曲线能够触碰到红色曲线，该点即与求最大似然有一样的意义。

具体公式放缩请看[这篇博客](https://www.cnblogs.com/bigmoyan/p/4550375.html "这篇博客")。
![GMM](https://img-blog.csdnimg.cn/20190601222951646.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 参数估计
如下图所示为 x 的概率分布。需要估计三个参数，分别是π，μ和Σ。
![GMM](https://img-blog.csdnimg.cn/20190601223631948.png)
分别计算出各自参数的似然函数：
如下图所示为参数 μ 的最大似然函数，具体推导可查看[这篇博客](https://blog.csdn.net/jinping_shi/article/details/59613054 "这篇博客")。
![GMM](https://img-blog.csdnimg.cn/20190601223942794.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
如下图所示为 Σ 的似然函数：
![GMM](https://img-blog.csdnimg.cn/20190601224253771.png)
最后是 π 的最大似然，由于 π 拥有限制条件 π1+π2+...+πk = 1，所以需要引入拉格朗日算子，公式如下：
![GMM](https://img-blog.csdnimg.cn/20190601224526322.png)
计算上述公式的最大似然函数为：
![GMM](https://img-blog.csdnimg.cn/20190601224625143.png)
最后得出：
具体推导可查看[这篇博客](https://blog.csdn.net/jinping_shi/article/details/59613054 "这篇博客")。
![GMM](https://img-blog.csdnimg.cn/20190601224655406.png)
具体EM函数步骤如下：
1. 根据初始的参数计算 z 的后验分布
2. 利用 z 的后验分布并带入三个不同的最大似然函数求得新的参数
3. 不断迭代直到收敛

__Reference:__
https://blog.csdn.net/jinping_shi/article/details/59613054
https://www.cnblogs.com/bigmoyan/p/4550375.html