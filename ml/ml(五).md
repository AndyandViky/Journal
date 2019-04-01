

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