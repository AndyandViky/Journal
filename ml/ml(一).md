# ml入门（一）powered by @李宏毅

-----------------------------------
![map](https://img-blog.csdnimg.cn/20190327232039762.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
## 第一课（机器学习总揽）
### ml基本步骤
<strong>1 找出一个function set</strong>
<strong>2 让maching拥有衡量一个function好坏的能力</strong>
&emsp;loss function L:  
&emsp;input: a function output: how bad it is  
&emsp;for example: square error  
![loss-example](https://img-blog.csdnimg.cn/20190327232107291.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
<strong>3 使用一个好的演算法挑出最好的那个function</strong>
&emsp;find a function with min loss  
&emsp;f* = argmin L(f)
### ml category
##### supervised learning // learning from teacher
<strong>1 regression</strong>
&emsp;the output of the target function f is 'scalar'  
<strong>2 classification</strong>
&emsp;_binary classification_  
&emsp;&emsp;the output of the target function f is 'Yes or No'  
&emsp;_multi-class classification_  
&emsp;&emsp;the output of the target function f is one of the 'class1, class2, .... classN'  
&emsp;_structured learning_  
&emsp;&emsp;the output of the target function f is a structural output  
##### semi-supervised learning
##### transfer learning
##### unsupervised learning
##### reinforcement learning // learning from critics

## 第二课（regression）
### gradient descent
<strong>1 for example</strong>
```
linear model y = w*x + b; w为权值，b为偏差， 目标是利用梯度下降求得最佳参数w和b;
// ps: 所谓线性模型是指，函数的参数对于输出来说是否为线性;
w*, b* = argmin L(w, b)
# consider loss function L(w, b) with two parameter w, b:
first: (Randomly) pick an initial value w0, b0
second: 分别为w，b在w=w0，b=b0的下求偏导
third: if the value is negative w1 = w0-r(@L/@w|w=w0,b=b0)， b1 = b0-r(@L/@b|w=w0,b=b0)其中r称为学习比率（@暂且代表求偏导符号）
four: loop second to third
five: get average error 每个点到所求出的损失函数的距离之和的平均值
last: get best w, b
// ps: 此处的best不一定代表的为global min, 有可能在某一点偏导数都为0但却不是global min。
```
<strong>2 learning rate</strong>
&emsp;rate太大loss可能会快速增大，rate太小可能收敛速度很慢 
&emsp;<strong>rate选取的解决办法</strong>
&emsp;&emsp;1 使用动态rate，每一次迭代都根据固定公式减小rate， 因为越到后面rate应该越小，精确度才会更高
&emsp;&emsp;2 不同的参数给予不同的learning rate          
&emsp;<strong>计算rate的方法</strong>
&emsp;&emsp;1 adagrad . 
&emsp;&emsp;每次计算所有example的loss, 之后更新参数
![learning-rate](https://img-blog.csdnimg.cn/20190327232317122.png)
![learning-rate1](https://img-blog.csdnimg.cn/20190327232343782.png)
![详细公式](https://img-blog.csdnimg.cn/20190324152204316.png) 
<strong>3 stochastic gradient descent</strong>
&emsp;每次只考虑一个example，算完一个example的loss就更新一次参数
 ![stochastic](https://img-blog.csdnimg.cn/20190324155614359.png)
<strong>4 feature scaling</strong>
&emsp;由于各个特征的输入值不同，单位不同，导致每个特征参数的改变对loss的影响都不同，所以需要  
&emsp;将输入值进行标准化，使得所有特征参数的改变对loss的影响相同或者相差不大。

<strong>5 overfitting(过拟合) // 在train_data上的效果好，但是在test_data上效果很差</strong>
&emsp;ps: overfitting是由于variance过大造成的，bias过大造成underfitting

<strong>6 regularization(目的在于寻找更加平滑的函数,防止过拟合)</strong>
&emsp;通常做法为在损失函数后加上一项约束项，权重的绝对值大小就会整体倾向于减小，尤其是不会出现特别大的值

## 第三课（error）
训练之后的使用L()求出error来自于biase和variance,知道错误的来源往往可以准确地improve model  
 ps: model越复杂biase越小，variance越大。model越简单biase越大，variance越小
<strong>如何确定error来源</strong>
&emsp;1 如果模型不能fit训练数据，代表bias大
&emsp;&emsp;可能需要去重新设计model(考虑更多的feature或者让model更加复杂)
![big-bias](https://img-blog.csdnimg.cn/20190327232413259.png)
&emsp;2 如果模型较好地fit了训练数据，但是在测试数据上错误很大，代表variance很大
&emsp;&emsp;1 可以加大训练数据量(非常有效但是并不是每一次都有用)
&emsp;&emsp;2 regularization
 ![small-bias](https://img-blog.csdnimg.cn/20190327232431808.png)

## 第四课（classification）
<strong>贝叶斯分类</strong>
利用先验概率和联合概率分布求出后验概率（即每次输入数据在各个分类下的概率，概率最大的即为当前预测分类）
![贝叶斯公式](https://img-blog.csdnimg.cn/20190325135330464.png)
&emsp;1 每一个类别拥有自己的协方差
&emsp;&emsp;在高斯分布(假设)下计算条件概率p(A|B)
![高斯密度函数](https://img-blog.csdnimg.cn/20190325140054936.png)
![最大似然函数求法](https://img-blog.csdnimg.cn/20190325140337187.png)
![结果1](https://img-blog.csdnimg.cn/20190327232502598.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;2 共用一个协方差
 ![共用一个协方差](https://img-blog.csdnimg.cn/20190325142114625.png)
 ![结果2](https://img-blog.csdnimg.cn/2019032723252487.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;3 sigmoid函数变换过程
&emsp; sigmoid函数详解 __https://blog.csdn.net/chinagreenwall/article/details/81113539__
![sigmoid函数变换](https://img-blog.csdnimg.cn/20190325143324451.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190325144705931.png)

## 第五课（logistic regression）
<strong>交叉熵</strong>
&emsp;概念详解 __https://blog.csdn.net/tsyccnh/article/details/79163834__

<strong>判别式模型与生成式模型</strong>
&emsp;判别式模型（Discriminative Model）是直接对条件概率p(y|x;θ)建模。常见的判别式模型有 线性回归模型、线性判别分析、支持向量机SVM、神经网络等。  
&emsp;生成式模型（Generative Model）则会对x和y的联合分布p(x,y)建模，然后通过贝叶斯公式来求得p(yi|x)，然后选取使得p(yi|x)最大的yi 

<strong>multi-class classification</strong>
![多分类流程图](https://img-blog.csdnimg.cn/20190325164318844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190325164849791.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;ps: softmax // 将多分类的输出数值转化为相对概率
&emsp;概念 __https://blog.csdn.net/red_stone1/article/details/80687921__

<strong>limitation of logistic regression // 无法对线性不可分数据进行分类</strong>
```
// 异或问题 example
[0, 1], [1, 0]为class1
[1, 1], [0. 0]为class2
// 在二维平面上是无法利用直线将不同的类别划分的
```
&emsp;ps: 可以使用feature transformation解决此类问题，即利用神经网络！！！![初识神经网络](https://img-blog.csdnimg.cn/20190325171115152.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
    