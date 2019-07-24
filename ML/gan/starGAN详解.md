
# starGAN知识小结
- - -
###### 本文为阅读完starGAN论文后作出的系统小结。[论文地址](https://arxiv.org/abs/1711.09020 "论文地址")
starGAN顾名思义就是星型GAN结构，其解决的主要问题为 multi-domain 的 image-to-image 转换。相比于传统的拥有 k 个 domain 的多领域图片转换需要训练 k(k-1) 个 generator ，效率极其低下，也无法充分地利用到训练数据，同时传统的解决方法也无法衔接多个数据集的 domains。基于上述问题，starGAN 被提出，starGAN 的基本思想为将 inputImage 和 target domain (一般为随机产生，使用binary 或者 one-hot vector 表示)一起作为 Generator 的输入得到 fakeImage，再将 fakeImage 和 origin domain 的值作为同一个 Generator 的输入得到 reconstructed image ，starGAN 希望的是 reconstructed image 和 inputImage 之间越接近越好。
下图为传统方法与 starGAN 的结构对比图，由图可以看出，starGAN 只需要训练一个Generator。 
![starGAN结构图](https://img-blog.csdnimg.cn/20190514133503116.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
下图为 starGAN 训练详细结构图，由图看出 Discriminator 不仅需要判断 fakeImage 的真伪还需要判断出 fakeImage 所处于的 domain。
![starGAN结构图](https://img-blog.csdnimg.cn/2019051413491293.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
## 损失计算
### 1 Adversarial Loss
下图为 Loss 的详细公式，其中 Discriminator 需要去最大化 D(x) (x 为 Generator 产生的图片) 的 score，意思是 D 需要尽可能正确地识别 x。而 G 需要最小化 1-D(G(x, c))，意思是 G 需要产出尽可能真实的图片使得 D(G(x, c)) 越大越好。
![Adversarial Loss](https://raw.githubusercontent.com/stdcoutzyx/Blogs/master/blog2017/starGAN/4.png)
### 2 Domain Classification Loss
对于给定的输入图像x和目标域标签c，我们的目标是将x转换为输出图像y，并将其正确地分类为目标域c。为了达到这个条件，添加了一个辅助分类器去训练 D。
该损失(cross-entropy)分为两部分：
1. D 在分类真实数据时所产生的损失：
![cross-entropy](https://raw.githubusercontent.com/stdcoutzyx/Blogs/master/blog2017/starGAN/5.png)
2. D 在分类 G 产生的假数据时产生的损失：
![cross-entropy](https://raw.githubusercontent.com/stdcoutzyx/Blogs/master/blog2017/starGAN/6.png)
在一般情况下需要现将 D 训练一段时间之后才能开始训练 G，因为在 D 效果很差的情况下 G 产生的数据无法分辨真假。
### 3 Reconstruction Loss
在最小化上述两种损失的情况下并不能保证在图片进行 translation 的时候只改变与领域有关的部分而保存其他部分。为解决这个问题，引入 Reconstruction Loss， 目的在于使 inputImage 和重建出来的 image (利用 fakeImage 与 origin-domain 产生出来的图片)越相近越好。
如下图所示，x 为 inputImage，c 为 target-domain， c` 为 origin-domain。
![L-1](https://raw.githubusercontent.com/stdcoutzyx/Blogs/master/blog2017/starGAN/7.png)
__汇总后为:__
![](https://raw.githubusercontent.com/stdcoutzyx/Blogs/master/blog2017/starGAN/8.png)
## 多数据集训练
在多个 dataset 的情况下提出 mask vector，mask vector 是一个 one-hot vector，表示的为当前提取的数据来源，大小为 dataset 的数量，例如 Celeba，RaFD 两个数据集，若当前提取的为 Celeba 的数据那么 RaFD 的 label 都将被置0。
如下图所示，根据 mask vector 区别不同的 dataset。
![starGAN示例](https://img-blog.csdnimg.cn/20190514142657916.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)