

# ml入门（三）powered by @李宏毅

-----------------------------------

## 第七课（cnn）
![cnn流程图](https://img-blog.csdnimg.cn/20190326194716526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### keras example
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
### 运行过程图片形状变化图
![运行过程图片形状变化图](https://img-blog.csdnimg.cn/20190327181102981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### convolution
<strong>filter卷积核</strong>  
### max pooling
![maxpooling](https://img-blog.csdnimg.cn/20190327171907510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![池化一次的结果](https://img-blog.csdnimg.cn/20190327172719134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### flatten
![flatten](https://img-blog.csdnimg.cn/20190327171802241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### full connection 
### shollw和deep的比较
![shollw和deep的比较](https://img-blog.csdnimg.cn/20190327174656887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
<strong>模块化</strong>  
利用较少的训练数据得到比较好的结果  
![模块化](https://img-blog.csdnimg.cn/20190327180603353.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

## 第八课（semi-supervised）
### generative model
### low-density-separation
<strong>self-train</strong>  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190327174637224.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### entropy-base-regularization
![entropy-base-regularization](https://img-blog.csdnimg.cn/20190327171824790.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### smoothness assumption
![smoothness assumption](https://img-blog.csdnimg.cn/2019032719101172.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![smoothness assumption](https://img-blog.csdnimg.cn/20190327191023817.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### better-representation

## 第九课（unsupervised learning）
### clustering
<strong>k-means</strong>  
![k-means](https://img-blog.csdnimg.cn/20190327210709576.png)
<strong>hierachical agglomerative clustering(HAC)层次聚类</strong>  
![hac](https://img-blog.csdnimg.cn/20190327210726775.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### dimension reduction
![dimension-reduction](https://img-blog.csdnimg.cn/2019032722104184.png)
<strong>feature selection</strong>
![feature selection](https://img-blog.csdnimg.cn/201903272211058.png)
<strong>principle component analysis(PCA)</strong>

