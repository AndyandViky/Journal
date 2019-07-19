# 各类GAN特征描述
### DC-GAN：
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
### SA-GAN：
__structure:__

__paper:__

__contributions：__
1 利用self-attention机制更好地考虑全局信息。
2 利用spectral normalization和TTUR(two timescale update rule)使模型训练更加稳定。

__weakness：__
### Big-GAN:
__structure:__
__paper:__

__contributions：__

__weakness：__

### W-GAN：
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

### W-GAN-GP：
ps: 判别器最后一层去掉sigmoid。

W-GAN-GP的网络结构和GAN一样，损失函数替换成Wasserstein距离。
__paper__:  https://arxiv.org/abs/1704.00028
 
__contributions：__
1 提出了一种新的lipschitz连续性限制手法—梯度惩罚，避免了权重两极化的情况，解决了训练梯度消失梯度爆炸的问题（由于是对每个batch中的每一个样本都做了梯度惩罚，所以判别器中不建议使用 batch norm且激活函数一般不使用基于使用动量的优化算法）。
2 比标准WGAN拥有更快的收敛速度，并能生成更高质量的样本。
3 提供稳定的GAN训练方式，几乎不需要怎么调参，成功训练多种针对图片生成和语言模型的GAN架构。

### LSGAN：
__paper:__  https://arxiv.org/abs/1611.04076

__contributions：__
1 使用了最小二乘损失函数代替了GAN的损失函数，缓解了GAN训练不稳定和生成图像质量差多样性不足的问题。

__weakness:__
1 LSGAN对离群样本的惩罚机制要求所有的生成样本分布，导致样本生成的”多样性”降低, 生成的样本很可能只是对真实样本的简单”模仿”和细微改动。

### StarGAN:
__structure:__
![starGAN](https://img-blog.csdnimg.cn/20190719185658950.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__ https://arxiv.org/abs/1711.09020

__contributions：__
1 设计了一个新颖的网络架构可以在只训练一个G和一个D的情况下就可以实现多领域图像转换。
2 将各个领域的特征学习映射到一个vector实现特征控制！！！

### BEGAN:
__structure:__
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190719185027930.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__ https://arxiv.org/abs/1703.10717

__contributions：__
1 采用autoencoder结构的discriminator，使得收敛速度有所提升。
2 提供了一个超参数，这个超参数可以在图像的多样性和生成质量之间做均衡，并且能够使GAN训练的更加稳定。
3 一个评估GAN训练程度的方法（Wasserstein distance）。

### ClusterGAN:
__structure:__
![cluster-gan](https://img-blog.csdnimg.cn/20190719184937495.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__ https://arxiv.org/abs/1809.03627

__contributions：__
1 在网络结构中加入Encoder与G一起训练，可以有效地保存聚类结构信息。 
2 利用离散和连续潜在变量的混合，以在潜在空间中创建非光滑几何。
3 提出了一种适应离散连续混合的新型反向传播算法，以及一个显式反向映射网络，以获得给定数据点的潜变量，因为该问题是非凸的。 
4 联合训练GAN以及具有聚类特定损失的反向映射网络，以便投影空间中的距离几何反映变量的距离几何。

### GANMM:
__structure:__
![GANMM](https://img-blog.csdnimg.cn/20190719184952968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__paper:__  [http://lamda.nju.edu.cn/yuy/(X(1)S(bfxaly24rpylwn55a3emukjm))/GetFile.aspx?File=papers/ijcai18-GANMM.pdf](http://lamda.nju.edu.cn/yuy/(X(1)S(bfxaly24rpylwn55a3emukjm))/GetFile.aspx?File=papers/ijcai18-GANMM.pdf) 

__contributions：__
1 设计出一个高效的聚类网络结构。
2 使用ϵ-Expectation-Maximization 算法防止 EM 在 GAN 中过早收敛的问题。
3 设计了一个防止聚类不平衡的算法。

__weakness：__
1 每个类别都需要训练一个GAN，内存耗费过大，训练时间过长。

### DeLiGAN:
__structure:__
__paper:__

__contributions：__

__weakness：__
### VAE-GAN:
__structure:__
__paper:__

__contributions：__

__weakness：__
### PGAN:
__structure:__
__paper:__

__contributions：__

__weakness：__
### GM-GAN:
__structure:__
__paper:__

__contributions：__

__weakness：__

__References:__
https://blog.csdn.net/qq_25737169/article/details/78857788