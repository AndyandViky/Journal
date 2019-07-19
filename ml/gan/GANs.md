# 各类GAN特征描述
#### DC-GAN：
__structure:__
ps:（生成器的最后一层和判别器的第一层不加batch-norm）
![dcgan](https://img-blog.csdnimg.cn/20190719171219699.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

__paper__: http://arxiv.org/abs/1511.06434

__contributions：__
将网络应用于GAN的训练。
为GAN的训练提供了一个很好的网络拓扑结构。
表明生成的特征具有向量的计算特性。

__weakness：__
但是对GAN训练稳定性来说是治标不治本，没有从根本上解决问题，而且训练的时候仍需要小心的平衡G,D的训练进程，往往是训练一个多次，训练另一个一次。
#### SA-GAN：
__structure:__
__paper:__

__contributions：__
利用self-attention机制更好地考虑全局信息。
利用spectral normalization和TTUR(two timescale update rule)使模型训练更加稳定。
#### Big-GAN:
__structure:__
__paper:__

__contributions：__
#### W-GAN：
__structure:__
__paper__: https://arxiv.org/abs/1701.07875

__contributions：__
以Wasserstein距离作为收敛性的度量（以缩小Wasserstein距离为目标）。
利用lipschitz连续性限制手法-梯度裁减，使得训练更加稳定。
#### W-GAN-GP：
__structure:__
__paper__:
 
__contributions：__
以Wasserstein距离作为收敛性的度量（以缩小Wasserstein距离为目标）。
提出了一种新的lipschitz连续性限制手法—梯度惩罚，解决了训练梯度消失梯度爆炸的问题（判别器中不能使用 batch norm）。
#### StarGAN:
__structure:__
__paper:__

__contributions：__
#### BEGAN:
__structure:__
__paper:__

__contributions：__
#### ClusterGAN:
__structure:__
__paper:__

__contributions：__
#### GANMM:
__structure:__
__paper:__

__contributions：__
#### DeLiGAN:
__structure:__
__paper:__

__contributions：__
#### VAE-GAN:
__structure:__
__paper:__

__contributions：__
#### PGAN:
__structure:__
__paper:__

__contributions：__
#### GM-GAN:
__structure:__
__paper:__

__contributions：__

__References:__
https://blog.csdn.net/qq_25737169/article/details/78857788