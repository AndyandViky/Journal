# ml入门（三）powered by @李宏毅

-----------------------------------

## 第七课（cnn）
![cnn流程图](https://img-blog.csdnimg.cn/20190326194716526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 1 keras example
```
from keras import Convolution2D, Dense, models, MaxPooling2D, Flatten
import numpy as np

model = models.Sequential()
# Convolution2D 第一个参数代表25个filter，第二和第三代表filter形状为3*3
# input_shape表示输入图片的形状28*28，第三个1表示为黑白图片，若为彩色图片为3
model.add(Convolution2D(25, 3, 3, input_shape=(28, 28, 1)))

# 其中参数代表选取的矩阵大小
model.add(MaxPooling2D((2, 2)))

# filter的数目一般越来越多
model.add(Convolution2D(50, 3, 3))
model.add(MaxPooling2D((2, 2)))

model.add(Flatten())

# 全连接
model.add(Dense(output_dim=100, activation='relu'))
model.add(Dense(output_dim=10, activation='softmax'))

# 开始训练 ...以下省略
model.fit()
```
### 2 运行过程图片形状变化图
![运行过程图片形状变化图](https://img-blog.csdnimg.cn/20190327181102981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 3 convolution
图像中不同数据窗口的数据和卷积核（一个滤波矩阵）作内积的操作叫做卷积。其计算过程又称为滤波（filter)，本质是提取图像不同频段的特征。
##### (1) 卷积核
也称为滤波器filter，带着一组固定权重的神经元，通常是n*m二维的矩阵，n和m也是神经元的感受野。n*m 矩阵中存的是对感受野中数据处理的系数。一个卷积核的滤波可以用来提取特定的特征（例如可以提取物体轮廓、颜色深浅等）。通过卷积层从原始数据中提取出新的特征的过程又成为feature map(特征映射)。
### 4 max pooling
对输入的特征图进行压缩，一方面使特征图变小，简化网络计算复杂度；一方面进行特征压缩，提取主要特征。
![maxpooling](https://img-blog.csdnimg.cn/20190327171907510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![池化一次的结果](https://img-blog.csdnimg.cn/20190327172719134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 5 flatten
Flatten层用来将输入“压平”，即把多维的输入一维化，再丢入全连接层。
![flatten](https://img-blog.csdnimg.cn/20190327171802241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 6 full connection
全连接就是把以前的局部特征重新通过权值矩阵组装成完整的图，进而将图片进行分类。
![cnn全连接图](https://img-blog.csdnimg.cn/2019032819511599.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 7 shallow和deep的比较
由图看出，较深的网络和较浅的网络在参数量一样的情况下，较深的网络性能更强。  
![shollw和deep的比较](https://img-blog.csdnimg.cn/20190327174656887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (1) 模块化 
模块化就像子函数一样可以被多处调用但是只需在一处定义即可，而不需要在每处需要使用的地方写上完整的函数。
![模块化](https://img-blog.csdnimg.cn/20190327180603353.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
由图看出，模块化能使网络将特征进一步分化，后面的分类器可以根据前面基础分类器得出结果进一步分类，可以利用较少的训练数据得到比较好的结果。

## 第八课（semi-supervised）
### 1 generative model
##### EM算法
举例：问题描述：抽取100个男生和100个女生样本的身高，但是我们不知道抽取的那200个人里面的每一个人到底是从男生的那个身高分布里面抽取的，还是女生的那个身高分布抽取的。 用数学的语言就是，抽取得到的每个样本都不知道是从哪个分布抽取的。 这个时候，对于每一个样本，就有两个东西需要猜测或者估计： （1）这个人是男的还是女的？（2）男生和女生对应的身高的高斯分布的参数是多少？  
<strong>EM算法要解决的问题是</strong>： （1）求出每一个样本属于哪个分布 （2）求出每一个分布对应的参数  
![em思维图](https://img-blog.csdnimg.cn/20190328202105687.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
ps: EM算法对初始值较为敏感！
### 2 low-density-separation
低密度分离假设就是假设数据非黑即白，在两个类别的数据之间存在着较为明显的鸿沟，即在两个类别之间的边界处数据的密度很低（即数据量很好）。
##### (1) self-train
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190327174637224.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 3 entropy-base-regularization
基于熵的正则化
![entropy-base-regularization](https://img-blog.csdnimg.cn/20190327171824790.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
由图可知xu属于某一类的概率越大，其熵的值越小。
### 4 smoothness assumption
位于稠密数据区域的两个距离很近的样例的类标签相似，也就是说，当两个样例被稠密数据区域中的边连接时，它们在很大的概率下有相同的类标签；相反地，当两个样例被稀疏数据区域分开时，它们的类标签趋于不同。  
![smoothness assumption](https://img-blog.csdnimg.cn/2019032719101172.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![smoothness assumption](https://img-blog.csdnimg.cn/20190327191023817.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 5 better-representation
去芜存菁、化繁为简，找到潜在的因素，通常潜在的因素比较简单且能够更好的表示原先的事物。

## 第九课（unsupervised learning）
### 1 clustering
##### (1) k-means
K均值聚类算法是先随机选取K个对象作为初始的聚类中心。然后计算每个对象与各个种子聚类中心之间的距离，把每个对象分配给距离它最近的聚类中心。聚类中心以及分配给它们的对象就代表一个聚类。一旦全部对象都被分配了，每个聚类的聚类中心会根据聚类中现有的对象被重新计算。这个过程将不断重复直到满足某个终止条件。 
![k-means](https://img-blog.csdnimg.cn/20190327210709576.png)
<strong>如何计算各个种子聚类之间的距离？</strong>
&emsp;1 欧式距离
&emsp;二维平面上两点a(x1,y1)与b(x2,y2)间的欧氏距离：
![欧式公式](https://img-blog.csdnimg.cn/20190329092902326.png)
&emsp;2 曼哈顿距离 // 城市街区距离
![曼哈顿距离](https://img-blog.csdnimg.cn/20190329093111945.png)
##### (2) hierachical agglomerative clustering(HAC)层次聚类 
假设有5个样本，计算两两之间的相似度，将最相似的两个样本聚合在一起（比如第一个和第二个），再将剩下的4个聚合在一起，以此类推。  
![hac](https://img-blog.csdnimg.cn/20190327210726775.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 2 dimension reduction
降维就是一种对高维度特征数据预处理方法。降维是将高维度的数据保留下最重要的一些特征，去除噪声和不重要的特征，从而实现提升数据处理速度的目的。  
<strong>优点：</strong>
&emsp;1 使得数据集更易使用。
&emsp;2 降低算法的计算开销。
&emsp;3 去除噪声。
&emsp;4 使得结果容易理解。 
![dimension-reduction](https://img-blog.csdnimg.cn/2019032722104184.png)
##### (1) feature selection
详细概念： _https://blog.csdn.net/hren_ron/article/details/80914491_  
从N个特征中选择其中M（M<N）个子特征，并且在M个子特征中，准则函数可以达到最优解。 
特征选择想要做的是：选择尽可能少的子特征，模型的效果不会显著下降，并且结果的类别分布尽可能的接近真实的类别分别。  
1. 生成过程：生成候选的特征子集；
2. 评价函数：评价特征子集的好坏；
3. 停止条件：决定什么时候该停止；
4. 验证过程：特征子集是否有效；
![feature selection](https://img-blog.csdnimg.cn/201903272211058.png)
##### (2) principle component analysis(PCA)
详细概念：_https://blog.csdn.net/program_developer/article/details/80632779_  
PCA的主要思想是将n维特征映射到k维上，这k维是全新的正交特征也被称为主成分，是在原有n维特征的基础上重新构造出来的k维特征。PCA的工作就是从原始的空间中顺序地找一组相互正交的坐标轴，新的坐标轴的选择与数据本身是密切相关的。其中，第一个新坐标轴选择是原始数据中方差最大的方向，第二个新坐标轴选取是与第一个坐标轴正交的平面中使得方差最大的，第三个轴是与第1,2个轴正交的平面中方差最大的。依次类推，可以得到n个这样的坐标轴。通过这种方式获得的新的坐标轴，我们发现，大部分方差都包含在前面k个坐标轴中，后面的坐标轴所含的方差几乎为0。于是，我们可以忽略余下的坐标轴，只保留前面k个含有绝大部分方差的坐标轴。  
![pca理论](https://img-blog.csdnimg.cn/20190329114658614.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;<strong>数学推导：</strong>
![pca数学推导](https://img-blog.csdnimg.cn/2019032911473688.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;<strong>缺点</strong>
&emsp;1 unsupervised
![pca缺点一](https://img-blog.csdnimg.cn/20190328105826170.png)
&emsp;2 linear 
![pca缺点二](https://img-blog.csdnimg.cn/20190328105842643.png)
##### (3) NMF（non-negative matrix factorization，非负矩阵分解）
详细概念： _https://blog.csdn.net/u011089523/article/details/77340476_
NMF的思想：V=WH（W权重矩阵、H特征矩阵、V原矩阵），通过计算从原矩阵提取权重和特征两个不同的矩阵出来。属于一个无监督学习的算法，其中限制条件就是W和H中的所有元素都要大于0。  
![nmf](https://img-blog.csdnimg.cn/20190329113240170.png)
##### (4) word embedding
专门用于文字的降维方法
![word-embedding思想](https://img-blog.csdnimg.cn/20190328110518446.png)
![word-embedding](https://img-blog.csdnimg.cn/20190329115108380.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;<strong>How to exploit the context?</strong>
&emsp;1 count based
&emsp;![count-base原理](https://img-blog.csdnimg.cn/20190328113303589.png)
&emsp;2 perdition based
&emsp;用前一个词预测后一个词：
![1](https://img-blog.csdnimg.cn/2019032911563083.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;用前两个词预测后一个词：  
![2](https://img-blog.csdnimg.cn/20190329115647638.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;ps: 要共享参数，否则一个词会有两个词向量，共享参数还可以减少参数数量。 
&emsp;图中的梯度下降时参数更新公式可以保证对应参数一直相等。
##### (4) neighbor embedding
&emsp;1 locally linear embedding(LLE)
&emsp;&emsp;从原来的分布中找一点 不变。
![LLE](https://img-blog.csdnimg.cn/20190329120136157.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;&emsp;LLE没有明确的function做降维。 
&emsp;&emsp;LLE的好处是，就算不知道 ，也可以用LLE。 
&emsp;&emsp;要好好调neighbour的个数，刚刚好才会得到好的结果。 
&emsp;&emsp;点之间的距离关系保持不变需要点之间够近，所以k太大不行，会考虑一些太远的点。

&emsp;2 T-distributed Stochastic Neighbor Embedding(t-sne)
&emsp;&emsp;前面方法只假设相近的点接近，未假设不相近的点要分开。 
&emsp;&emsp;t-SNE先分别计算原空间、新空间中所有点对的相似度再归一化。归一化是必要的，为了防止两个空间中距离的scale不同。 
&emsp;&emsp;然后最小化二者之间的KL距离，代入公式用GD即可。 
&emsp;&emsp;t-SNE计算量大，可先用PCA降维，再用t-SNE降维。 
&emsp;&emsp;t-SNE需要一堆data point，然后找到z。如果之后再给一个数据，t-SNE是没法做的，只能再跑一遍。 
&emsp;&emsp;所以t-SNE主要用于可视化。 
&emsp;&emsp;t-SNE的神妙之处在于原空间与新空间中相似度计算公式不同。 
&emsp;&emsp;原空间的计算公式，使得两个点的相似度随距离增加快速下降。 
&emsp;&emsp;而新空间的计算公式，是长尾的，为了维持相同的相似度，两个点之间要分得更开。对原来距离很近的点，影响很小。而原来离得远的点，会被拉开，强化gap。 
&emsp;&emsp;比较LLE on MNIST 与 t-SNE on MNIST。后者先做了PCA。
![t-sne](https://img-blog.csdnimg.cn/20190329120528566.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 3 deep auto-encoder
使用neural network做unsupervised learning,类似于PCA只不过hidden layer比PCA多 
![auto-encoder原理](https://img-blog.csdnimg.cn/20190328162909123.png)
##### (1) de-noising auto-encoder
为了验证encoder和decoder是否学习到东西
![de-noising auto-encoder](https://img-blog.csdnimg.cn/20190328162937689.png)
### 4 generative model
##### 1 PixelRNN
根据前面的像素预测接下来的像素。PixelRNN不仅work，而且在各种生成方法中PixelRNN产生的图是最清晰的。 
用在图像上有一些tips: 如果RGB三个值相差不大，则得到的颜色灰灰的、不够明亮，可以把众多颜色聚成若干类然后做1-of-N encoding。 
##### 2 variational autoencoder(VAE)
VAE得到的结果不太清楚。VAE与PixelRNN区别在于，理论上VAE可以控制要生成的image。  
比如code是10维，固定其中8维、调整剩余2维，看结果，可以解读code的每个维度代表什么意思，从而每个维度就像拉杆一样可以有目的的调整。  
![VAE](https://img-blog.csdnimg.cn/20190328165852395.png)
##### 3 generative adversarial network(GAN)