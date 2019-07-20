# 各类GAN特征描述
### 1）DC-GAN：
__structure:__
ps:（生成器的最后一层和判别器的第一层不加batch-norm）
![dcgan](https://img-blog.csdnimg.cn/20190719171219699.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

__paper__: http://arxiv.org/abs/1511.06434

__contributions：__
1 将网络应用于GAN的训练。
2 为GAN的训练提供了一个很好的网络拓扑结构。
3 表明生成的特征具有向量的计算特性。

__weakness：__
1 但是对GAN训练稳定性来说是治标不治本，没有从根本上解决问题，而且训练的时候仍需要小心的平衡G,D的训练进程，往往是训练一个多次，训练另一个一次。

### 2）SA-GAN：
__structure:__
![SA-GAN](https://img-blog.csdnimg.cn/20190720161623113.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__  https://arxiv.org/abs/1805.08318

__contributions：__
1 利用self-attention机制更好地考虑全局信息。
2 利用spectral normalization和TTUR(two timescale update rule)使模型训练更加稳定。
3 根据self-attention机制可以生成质量非常高的数据。

### 3）Big-GAN:
__structure:__
![B](https://img-blog.csdnimg.cn/20190720163540754.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__ https://arxiv.org/abs/1809.11096

__contributions：__
1 通过2-4倍的增加参数量（增加channel），8倍的扩大batchsize，可以使GAN获得最大的性能提升。
2 通过使用截断技巧（truncation trick），可以使得训练更加平稳，但是需要在多样性和逼真度之间做平衡。
3 通过现存的和其他新颖的各种技术的集合，可以保证训练的平稳性，但是精度也会随之下降，需要在性能和训练平稳性之间做平衡。

__负面影响:__
Reference: https://blog.csdn.net/qq_14845119/article/details/85619705
1 增加网络深度会使得精度降低。
2 在判别器上使用贡献嵌入参数的方法，对参数的选择非常敏感，刚开始有助于训练，后续则很难优化。
3 使用WeightNorm 替换BatchNorm 会使得训练难以收敛，去掉BatchNorm 只有Spectral Normalization 也会使得难以收敛。
4 判别器中增加BatchNorm 会使得训练难以收敛。
5 在128*128的输入情况下，改变attention block对精度没提升，在256*256输入的情况下，将attention block上移一级，会对精度有提升。
6 相比采用3*3的滤波器，采用5*5的滤波器会使精度有略微提升，而7*7则不会。
7 使用膨胀卷积会降低精度
8 将生成器中的最近邻插值换为双线性插值会使得精度降低。
9 在共享嵌入中使用权值衰减（weight decay），当该衰减值较大（10-6 ）会损失精度，较小（10-8 ）会起不到作用，不能阻止梯度爆炸。
10 在类别嵌入中，使用多层感知机（MLP）并不比线性投影（linear projections）好。
11 梯度归一化截断会使得训练不平稳。

### 4）W-GAN：
ps: 判别器最后一层去掉sigmoid。

W-GAN的网络结构和GAN一样，损失函数替换成Wasserstein距离。

__paper__: https://arxiv.org/abs/1701.07875

__contributions：__
1 以Wasserstein距离作为收敛性的度量（以缩小Wasserstein距离为目标）。
2 利用lipschitz连续性限制手法-梯度裁减，使得训练更加稳定。
3 解决了模式崩溃的（collapse mode）问题，生成结果多样性更丰富。
4 对GAN的训练提供了一个收敛指标。

__weakness：__
1 强制裁减梯度会造成权重的两极化，权重的值大部分分布在边界处，这对于深度神经网络来说不能充分发挥深度神经网络的拟合能力；并且，也发现强制剪切权重容易导致梯度消失或者梯度爆炸，梯度消失很好理解，就是权重得不到更新信息，梯度爆炸就是更新过猛了，权重每次更新都变化很大，很容易导致训练不稳定。

### 5）W-GAN-GP：
ps: 判别器最后一层去掉sigmoid。

W-GAN-GP的网络结构和GAN一样，损失函数替换成Wasserstein距离。
__paper__:  https://arxiv.org/abs/1704.00028
 
__contributions：__
1 提出了一种新的lipschitz连续性限制手法—梯度惩罚，避免了权重两极化的情况，解决了训练梯度消失梯度爆炸的问题（由于是对每个batch中的每一个样本都做了梯度惩罚，所以判别器中不建议使用 batch norm且激活函数一般不使用基于使用动量的优化算法）。
2 比标准WGAN拥有更快的收敛速度，并能生成更高质量的样本。
3 提供稳定的GAN训练方式，几乎不需要怎么调参，成功训练多种针对图片生成和语言模型的GAN架构。

### 6）LSGAN：
__paper:__  https://arxiv.org/abs/1611.04076

__contributions：__
1 使用了最小二乘损失函数代替了GAN的损失函数，缓解了GAN训练不稳定和生成图像质量差多样性不足的问题。

__weakness:__
1 LSGAN对离群样本的惩罚机制要求所有的生成样本分布，导致样本生成的”多样性”降低, 生成的样本很可能只是对真实样本的简单”模仿”和细微改动。

### 7）StarGAN:
__structure:__
![starGAN](https://img-blog.csdnimg.cn/20190719185658950.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__ https://arxiv.org/abs/1711.09020

__contributions：__
1 设计了一个新颖的网络架构可以在只训练一个G和一个D的情况下就可以实现多领域图像转换。
2 将各个领域的特征学习映射到一个vector实现特征控制！！！

### 8）BEGAN:
__structure:__
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190719185027930.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__ https://arxiv.org/abs/1703.10717

__contributions：__
1 采用autoencoder结构的discriminator，使得收敛速度有所提升。
2 提供了一个超参数，这个超参数可以在图像的多样性和生成质量之间做均衡，并且能够使GAN训练的更加稳定。
3 使用Wasserstein distance评估模型。

### 9）ClusterGAN:
__structure:__
![cluster-gan](https://img-blog.csdnimg.cn/20190719184937495.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__ https://arxiv.org/abs/1809.03627

__contributions：__
1 在网络结构中加入Encoder与G一起训练，可以有效地保存聚类结构信息。 
2 利用离散和连续潜在变量的混合，以在潜在空间中创建非光滑几何。
3 提出了一种适应离散连续混合的新型反向传播算法，以及一个显式反向映射网络，以获得给定数据点的潜变量，因为该问题是非凸的。 
4 联合训练GAN以及具有聚类特定损失的反向映射网络，以便投影空间中的距离几何反映变量的距离几何。

### 10）GANMM:
__structure:__
![GANMM](https://img-blog.csdnimg.cn/20190719184952968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__  [http://lamda.nju.edu.cn/yuy/(X(1)S(bfxaly24rpylwn55a3emukjm))/GetFile.aspx?File=papers/ijcai18-GANMM.pdf](http://lamda.nju.edu.cn/yuy/(X(1)S(bfxaly24rpylwn55a3emukjm))/GetFile.aspx?File=papers/ijcai18-GANMM.pdf) 

__contributions：__
1 设计出一个高效的聚类网络结构。
2 使用ϵ-Expectation-Maximization 算法防止 EM 在 GAN 中过早收敛的问题。
3 设计了一个防止聚类不平衡的算法（根据当前各个聚类的情况在下一轮次提供不同数量的训练数据）。

__weakness：__
1 每个类别都需要训练一个GAN，内存耗费过大，训练时间过长。

### 11）DeLiGAN:
__structure:__
![DELIGAN](https://img-blog.csdnimg.cn/20190720144725285.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__ https://arxiv.org/abs/1706.02071

__contributions：__
1 提出一种GAN架构可以非常好地训练数据量小并且多样性多的数据集。
2 结合高斯混合模型对输入latent $z$做一个线性变换$z=\mu_{k}+\sigma_{k}*z$。
3 对不同的数据集使用不同的架构（个人感觉这并不是很好）。

__weakness:__
1 整体感觉有种玄学的感觉。

### 12）VAE-GAN:
__structure:__
![VAE-GAN](https://img-blog.csdnimg.cn/20190720150828984.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__  https://arxiv.org/abs/1806.00509

__contributions：__
1 结合VAE和GAN，利用AE可以得到一个比较好的latent $z$。
2 经由较好的$z$可以得到质量较高的图像。

### 13）PGAN:
__structure:__
![PGAN](https://img-blog.csdnimg.cn/20190720152950590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__  https://arxiv.org/abs/1708.01886

__contributions：__
1 D由Encoder和GMM组成，利用高斯混合模型去评估fake-image和real-image之间的差别，对于真实数据需要给出一个高的概率，对以虚假数据需要给出一个低的概率。

### 14）GM-GAN:
__paper:__ https://arxiv.org/abs/1808.10356

__contributions：__
1 将GMM作为latent $z$的样本集合，每次$z$从某个高斯模型中sample。
2 利用scaling factor $\sigma$来控制生成样本的多样性和质量之间的关系，其中公式为$z|k ∼ N (μ_{k} , σ ∗ Σ_{k} )$，$\sigma$越大多样性越强反而质量有所下降。