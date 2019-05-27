# 空间转换网络(Spatial Transformer Networks)
---
普通的CNN能够显示的学习平移不变性，以及隐式的学习旋转不变性，但attention model 告诉我们，与其让网络隐式的学习到某种能力，不如为网络设计一个显式的处理模块，专门处理以上的各种变换。

空间变换网络，允许神经网络学习如何在输入图像上执行空间变换，以增强模型的几何不变性，意思就是在对样本内容作微小的移动之后不会改变其原本的输出。
例如，它可以裁剪感兴趣的区域，缩放并校正图像的方向。
只需要在现有CNN上做微小的调整就能实现空间变换网络。

直观来说就是在CNN网络前添加了一层transform layer，在读取图片之后对其做一个转换，将转换之后的图片作为输入丢入CNN结构。

### 仿射变换（Affine transformation）
仿射变换的意思就是一张图在原图的基础上经过平移，缩放，旋转，剪切等相关操作。
综合可得公式：
![stn](https://img-blog.csdnimg.cn/20190527215542872.png)
如上图所示，将 a,b,c,d,e,f 代入不同值可以实现不同的仿射变换。
### 双线性插值（Bilinear Interpolation）
__线性插值__：在已知两点的情况下(x1, y1), (x2, y2)，线性插值的意思就是求某点x  (x1<=x<=x2) 在两点连成的直线上的 y 值。
公式如下图所示，即直线两点式方程。
![stb](https://img-blog.csdnimg.cn/20190527215355611.png)
__双线性插值__：双线性插值的意思就是在已知四点的情况下求中间某点的 y 值。
如下图所示，求未知点p的y值。
![stn3](https://img-blog.csdnimg.cn/20190527213721385.png)
先横向做两次线性插值，得出R1，R2，再根据R1，R2两点做纵向线性插值，得到p点的y值。
![stn](https://img-blog.csdnimg.cn/2019052721524937.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
__由于图片中的每个像素点都为整数，为了保证图片的变换后的真实性可以使用双线性插值，双线性插值的作用在于利用公式计算出新图的像素值。__
### 网络结构
![stn](https://img-blog.csdnimg.cn/20190527220619148.png)
由上图所示：STN 由三部分组成
1. Localisation Network-局部网络，__目的在于进行仿射变换__
2. Parameterised Sampling Grid-参数化网格采样，__目的在于计算转换后的像素值__
3. Differentiable Image Sampling-差分图像采样，__目的在于利用期望的插值方式计算出实际的像素值__

__1. Localisation Network-局部网络__
输入：特征图

输出：变换矩阵 ，用于下一步计算（ 输出规模视具体的变换。以仿射变换为例， 是一个[2,3]大小的6维参数）

注： θ 被初始化为恒等变换矩阵，通过损失函数不断更正 θ 的参数，最终得到期望的仿射变换矩阵。得到输出特征图后最重要的是得到输出特征图每个位置的像素值。

__2. Parameterised Sampling Grid-参数化网格采样__
此部分的目的在于计算出新图中的像素点。
![stn](https://img-blog.csdnimg.cn/201905272237042.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
计算公式如下，即仿射变换公式：
![stn](https://img-blog.csdnimg.cn/20190527223811855.png)
由于计算出来的值可能不为整数，所以需要使用双线性插值计算出各个点的像素值。

__3.Differentiable Image Sampling-差分图像采样__
该部分目的在于利用期望的插值方式计算出真正的像素值。
具体步骤可参见前面提到的双线性插值计算流程。
下图为双线性插值总公式：
![stn](https://img-blog.csdnimg.cn/20190527224301320.jpg)
该式子等价于：
![stn](https://img-blog.csdnimg.cn/20190527224455487.jpg)
测试结果图如下所示：
![stn](https://img-blog.csdnimg.cn/2019052722463016.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
完整源码：
https://github.com/AndyandViky/ML-study/blob/master/pytorch/spatial-transformer-networks.py
Reference:
https://zhuanlan.zhihu.com/p/37110107
https://blog.csdn.net/qq_39422642/article/details/78870629
https://blog.csdn.net/xbinworld/article/details/65660665
https://blog.csdn.net/xiaqunfeng123/article/details/17362881


