

# ml入门（二）powered by @李宏毅

-----------------------------------

## 第六课（deep learning）
![结构图](https://img-blog.csdnimg.cn/20190325200204876.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 一 前向传播和反向传播
概念详解 __https://blog.csdn.net/bitcarmanlee/article/details/78819025__
### 二 keras实战
```
// example
from keras.layers import Dense, models
model = models.Sequential() // 初始化模型

// a layer instance is callable on a tensor, and returns a tensor
// Dense表示添加的为全连接layer
// input_dim为输入数据的形状， units为输出单元数， activation为激活函数
model.add(Dense(input_dim=28*28, units=500, activation='relu'))
model.add(Dense(units=500, activation='relu'))
model.add(Dense(units=10, activation='softmax'))

// loss指定损失函数, optimizer指定优化函数, matrics用来指定衡量模型的指标
model.compile(loss='categorical_crossentropy', optimizer='adadelta', matrics=['accuracy'])

// x_train为输入数据， y_train为输入标签， batch_size为每次训练数， nb_epoch为训练轮数
model.fit(x_train, y_train, batch_size=100, nb_epoch=10)

// case1: 已知训练数据的y，测试一下model的好坏， score返回一个二维向量，score[0]为总损失，score[1]为精确度
score = model.evaluate(x_test, y_test)

// case2: 直接进行预测
result = model.predict(x_test)
```
### 三 如何优化model
#### 使用train_data结果较差
(1) 更换activation function  
(2) 调整learning rate  
(3) RMSProp+momentum = adam 动态调整learning rate  
##### RMSProp（Root Mean Square Prop）
RMSProp算法对梯度计算了微分平方加权平均数。这种做法有利于消除了摆动幅度大的方向。  
微分平均加权平均数：根据同一个移动段内不同时间的数据对预测值的影响程度，分别给予不同的权数，然后再进行平均移动以预测未来值
```
v0＝0
v1=βv0+(1−β)x1
v2=βv1+(1−β)x2
......
```
![rmsprop的计算方法](https://img-blog.csdnimg.cn/20190326155056681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### momentum
类似于物理中的惯性原理，将上一轮的所前进方向作为动量，最后根据本轮的梯度和动量计算出本轮实际的运动方向  
![momentum思想图](https://img-blog.csdnimg.cn/20190326155141304.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
#### overfitting解决办法
<strong>(1) early stopping</strong> 

基本含义是在训练中计算模型在验证集上的表现，当模型在验证集上的表现开始下降的时候，停止训练，这样就能避免继续训练导致过拟合的问题  

<strong>(2) regularization</strong>

范数详细概念： _https://blog.csdn.net/zwqjoy/article/details/81912821_
<strong>L1范数(Lasso Regression) // 向量各元素的绝对值之和</strong>
L1规范化经常被用在降噪声和图像重建中  
![L1](https://img-blog.csdnimg.cn/20190326165241747.png)
<strong>L2范数(Ridge Regression) // 欧氏距离的平方乘上一个系数 别名：权重衰减（weight decay）</strong>
根据公式形式，这样一项加上之后，权重的绝对值大小就会整体倾向于减小，尤其是不会出现特别大的值  
![L2](https://img-blog.csdnimg.cn/20190326165155433.png)
<strong>L1和L2的区别</strong>
L1可以让一部分特征的系数缩小到0，从而间接实现特征选择。所以L1适用于特征之间有关联的情况。  
L2让所有特征的系数都缩小，但是不会减为0，它会使优化求解稳定快速。所以L2适用于特征之间没有关联的情况。  

<strong>(3) dropout</strong>
Dropout说的简单一点就是：我们在前向传播的时候，让某个神经元的激活值以一定的概率p停止工作，这样可以使模型泛化性更强，因为它不会太依赖某些局部的特征。
<strong>过程：</strong>
1. 首先随机（临时）删掉网络中一半(一般情况下，此参数可自定义)的隐藏神经元，输入输出神经元保持不变。
2. 然后把输入x通过修改后的网络前向传播，然后把得到的损失结果通过修改的网络反向传播。一小批训练样本执行完这个过程后，在没有被删除的神经元上按照随机梯度下降法更新对应的参数（w，b）。
3. 然后继续重复这一过程：
   1. 恢复被删掉的神经元（此时被删除的神经元保持原样，而没有被删除的神经元已经有所更新）。
   2. 从隐藏层神经元中随机选择一个一半大小的子集临时删除掉（备份被删除神经元的参数）。
   3. 对一小批训练样本，先前向传播然后反向传播损失并根据随机梯度下降法更新参数（w，b） （没有被删除的那一部分参数得到更新，删除的神经元参数保持被删除前的结果）。

![dropout流程图](https://img-blog.csdnimg.cn/20190326155353732.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![dropout注意事项](https://img-blog.csdnimg.cn/20190326155420764.png)
#### 梯度消失问题
当前面隐藏层的学习速率低于后面隐藏层的学习速率，即随着隐藏层数目的增加，分类准确率反而下降了。这种现象叫做消失的梯度问题。  
使用sigmoid function在layer嵌套过深的情况下会造成梯度不断衰减最后趋近于零。
  
<strong>relu(Rectified Linear Unit)</strong>
![relu](https://img-blog.csdnimg.cn/20190326172252341.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
可以看出，relu函数的导数在正数部分是恒等于1的，因此在深层网络中使用relu激活函数就不会导致梯度消失的问题。
### 四 maxout 激活函数
_使用maxout的默认先验：样本集是凸集可分的_
在前馈式神经网络中，Maxout的输出取该层的多个fc的同一位置的神经元的最大值；卷积中，Maxout的输出取多个feature map同一位置的最大值。
![maxout](https://img-blog.csdnimg.cn/20190326174051745.png) 
maxout 可以拟合任意的的凸函数。  
![凸函数举例](https://img-blog.csdnimg.cn/20190326174414988.png)