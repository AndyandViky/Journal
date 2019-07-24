<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
# ml入门（五）powered by @李宏毅

-----------------------------------

## 第十三课（RNN,recurrent neural network，循环神经网络）
全连接神经网络和卷积神经网络他们都只能单独的取处理一个个的输入，前一个输入和后一个输入是完全没有关系的。但是，某些任务需要能够更好的处理序列的信息，即前面的输入和后面的输入是有关系的。比如，当我们在理解一句话意思时，孤立的理解这句话的每个词是不够的，我们需要处理这些词连接起来的整个序列；当我们处理视频的时候，我们也不能只单独的去分析每一帧，而要分析这些帧连接起来的整个序列。这时，就需要用到深度学习领域中另一类非常重要神经网络：循环神经网络(Recurrent Neural Network)。
![RNN](https://img-blog.csdnimg.cn/20190401165720622.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![RNN1](https://img-blog.csdnimg.cn/20190401165754591.png)
RNN会根据store中的内容和当前内容一同预测。不同的store会得出不同的结果，因此RNN对输入顺序比较敏感，不同的输入顺序得出的结果可能完全不同。

<strong>分类：</strong>
![RNN分类](https://img-blog.csdnimg.cn/20190401170227455.png)
Elman Network存储前一次的输入内容，Jordan Network存储前一次的输出内容。
### 1 Bidirectional RNN(双向RNN)
![BidirectionalRNN](https://img-blog.csdnimg.cn/20190401170438837.png)
同时进行正向和反向的训练，根据正向和反向的hidden layer共同得出一个output，这样做的好处是增大的RNN的视野，得到更好的performance。
### 2 Long Short-Term Memory(LSTM)
![LSTM](https://img-blog.csdnimg.cn/20190401171006653.png)
LSTM拥有三个Gate，三个Gate何时关闭何时打开都是maching自己学到的：
1. Input Gate
   决定输入时是否可以将当前内容存入store。
2. Output Gate
   决定输出时是否可以将store中的值取出一同判断。
1. Forget Gate
   决定是否需要将store中的内容清空。
   
<strong>结构图：</strong>
&emsp;<strong>1 LSTM cell：</strong>
&emsp;![LSTM结构图](https://img-blog.csdnimg.cn/20190401172212968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;其中有三个信号量分别为zi, zf, zo。 zi控制着inputGate是否开启，zf控制forgetGate是否开启，zo控制outputGate是否开启，这三个信号量的输入都经过sigmoid函数使其变为0到1之间的数。由图可知相关控制流程。

&emsp;<strong>2 LSTM 多cell结构图：</strong>
&emsp;![LSTM1](https://img-blog.csdnimg.cn/20190401175939564.png)
&emsp;输入的xt分别乘上不同的tansform得到4个不同的vector信号量分别输入到LSTM cell中。

&emsp;<strong>3 LSTM 详细结构图：</strong>
&emsp;![LSTM2](https://img-blog.csdnimg.cn/20190401180315373.png)
&emsp;由图可知，当前输入由三个vector控制，ct为memory里的数据，ht为上一轮准备输出的数据，xt为当前输入数据。这才是真正的LSTM。

<strong>LSTM与Neural Network的关系：</strong>
![LSTM2](https://img-blog.csdnimg.cn/20190401174208344.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
一个LSTM cell就相当于一个神经元，不同之处在一个普通NN只有一个输入和一个输出，但是LSTM cell有四个输入（并且四个输入都不是一样的）和一个输出。所以LSTM的参数量是一般NN参数的4倍。 

### 3 RNN的learning
RNN在Learning时如何定义loss function呢？以slot filling 为例，对每个输入xi, 其输出的yi 与相应的reference vector计算cross entropy, cross entropy之和就是loss function. 训练时word sequence不能打散，要先用x1 得到y1, 再用x2 得到y2. 
定义好loss function之后，用gradient descent做training。gradient descent的算法从BP进阶为BPTT（Backpropagation through time）. 
但是，很遗憾的，RNN的训练是比较困难的，其error surface要么很平缓要么很陡峭。这样在做gradient descent时，参数可能跳到悬崖上，造成loss暴增，或者跳到悬崖边上（gradient很大），造成参数飞出去(NAN)。解决办法是clipping: 当gradient＞某个阈值时，令gradient = 阈值。 
![RNN-learning](https://img-blog.csdnimg.cn/20190401182604966.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
为何RNN会有rough的error surface？ 
并不是因为来自sigmoid的gradient vanish，因为换成ReLU也存在相同问题。（RNN中把sigmoid换成ReLU效果往往更差） 
其实是因为RNN处理的是time sequence, 同样的weight在不同时刻被多次使用。 
![LSTM-error](https://img-blog.csdnimg.cn/20190401194145838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
LSTM可以解决gradient vanishing的问题，但没有解决gradient explore问题，那么LSTM是如何解决gradient vanishing问题的呢？
在一般的RNN中，每个时刻neuron的output都会被放到memory中去，所以在每个时刻memory中的值都会被洗掉。但在LSTM中，是把memory中原来的值乘上一个数再加上一个数，即memory和input是想加的关系。所以LSTM中如果weight影响了memory中的值，那么这个影响会永远都存在（除非forget gate决定洗掉memory，有说法认为要给forget gate很大的bias以使其多数情况下开启），而不像SimpleRNN中memory的值在每个时刻都会被洗掉。 
若使用LSTM出现了过拟合，可考虑改用GRU。GRU的精神是“旧的不去，新的不来”，它将input gate与forget gate联动起来：若input gate 开，则forget gate 关。
### 4 RNN的应用
![RNN应用](https://img-blog.csdnimg.cn/20190401195555891.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![RNN-应用1](https://img-blog.csdnimg.cn/20190401200031958.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
此应用无法处理相同字同时出现的情况。如何解决？
![RNN-应用11](https://img-blog.csdnimg.cn/20190401200213184.png)

## 第十四课（Ensemble）
在机器学习的有监督学习算法中，我们的目标是学习出一个稳定的且在各个方面表现都较好的模型，但实际情况往往不这么理想，有时我们只能得到多个有偏好的模型（弱监督模型，在某些方面表现的比较好）。集成学习就是组合这里的多个弱监督模型以期得到一个更好更全面的强监督模型，集成学习潜在的思想是即便某一个弱分类器得到了错误的预测，其他的弱分类器也可以将错误纠正回来。
### 1 bagging
用在很强的子model。
![Bagging](https://img-blog.csdnimg.cn/20190401205710858.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
当模型非常复杂且容易overfit时适合使用Bagging。目的在于减少variance。
##### 1 decision tree(决策树)
决策树非常容易overfitting。
![decsion-tree](https://img-blog.csdnimg.cn/20190401210222919.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### 2 random forest(随机森林)
决策树的bagging。
![random-tree](https://img-blog.csdnimg.cn/20190401210930562.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 2 boosting（推进）
用在较弱的子model且这些model无法去fit我们的data时。
boosting训练子分类器时是有顺序的！！
![boosting](https://img-blog.csdnimg.cn/20190401211634458.png)
##### (1) How to obtain different classifiers?
![get-different-classifier](https://img-blog.csdnimg.cn/20190401214346340.png)
根据当前轮次的结果动态地更新权值，效果好的train-data将其权值变小，效果不好的增大其权值，这样可以突出训练错误数据。
##### (2) adaboost
思想：首先利用一笔数据训练好一个f1(x),然后找到另一笔数据，其在f1(x)这个模型上训练效果很差，再用这笔数据训练f2(x).....循环下去
![adaboost](https://img-blog.csdnimg.cn/20190401220049215.png)
![adaboost1](https://img-blog.csdnimg.cn/20190401221050300.png)
![adaboost2](https://img-blog.csdnimg.cn/2019040122365438.png)
<strong>How to get d1?</strong>
![adaboost权重公式](https://img-blog.csdnimg.cn/20190401224522822.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
<strong>How to aggregate all classifier?</strong>
![aggregate them](https://img-blog.csdnimg.cn/20190401230107443.png)
<strong>error rate of final classifier</strong>
![error-rate](https://img-blog.csdnimg.cn/20190401231706225.png)
<strong>error rate 上限证明</strong>
![error rate 上限证明](https://img-blog.csdnimg.cn/20190401232320570.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
<strong>error rate 上限会缩小证明</strong>
![上限会缩小证明](https://img-blog.csdnimg.cn/20190401233157795.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

<strong>Gradient Boosting</strong>

&emsp;<strong>一般公式推导：</strong>
&emsp;![gradient-boosting](https://img-blog.csdnimg.cn/20190401234449694.png)
![公式推导](https://img-blog.csdnimg.cn/2019040210063786.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

##### (3) stacking
![stacking](https://img-blog.csdnimg.cn/20190402101130611.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
将多个model合起来作为final classifier的输入，由于前面的model可能非常复杂，所以final classifier可以简单一点。