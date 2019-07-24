<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
# DL入门（二）powered by @李宏毅

-----------------------------------

## 第四课（Sequence to Sequence）
![outline](https://img-blog.csdnimg.cn/20190405180654676.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 1 Sequence Generation
![Sequence Generation](https://img-blog.csdnimg.cn/20190405222622709.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
输入一个vector,输出也为一个vector。
### 2 Conditional Sequence Generation
产生符合某种情景的句子或者图片...
![Conditional Sequence Generation](https://img-blog.csdnimg.cn/20190405224026372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 3 Dynamic Conditional Sequence Generation
假如输入的是一篇非常长的文章，我们没有办法将一个非常大的vector多次输入到Decoder中。
![Dynamic Conditional Sequence Generation](https://img-blog.csdnimg.cn/20190405230028250.png)
<strong>example:</strong>
![Dynamic Conditional Sequence Generation1](https://img-blog.csdnimg.cn/20190405230345901.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 4 Tips of Generation
1. attention
在一个视频中存在许多的图片，那么machind可能会给某张图片中非常多的attention，例如下图，过于关注图二可能会产生多个woman，我们可以平均化每张图片的attention来试着解决这个问题。
![attention](https://img-blog.csdnimg.cn/20190405232608147.png)
2. exposure bias
如下图，在训练时maching可能只专门训练了某些‘路径’，在训练时maching可能会在第一步就选错，那么无论怎么样后续都不会正确。
![exposure bias](https://img-blog.csdnimg.cn/20190405234042859.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 5 Pointer Network
详细解析：_https://blog.csdn.net/dukuku5038/article/details/84112072_

## 第五课（Recursive Network）
Recursive Network需要自己决定network的structure，而 Recurrent Network是固定的structure，可以说RNN是Recursive Network的一个special case。
![Recursive Network](https://img-blog.csdnimg.cn/20190406093732246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
<strong>example:</strong>
核心在于找到一个f，可以正确地处理数据并且输出相应内容。f可以是NN。
![example](https://img-blog.csdnimg.cn/20190406094616925.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 1 Recursive Neural Tensor Network
主要在于 f 函数中包含了Tensor Network。
![Recursive Neural Tensor Network](https://img-blog.csdnimg.cn/2019040609532784.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 2 Tree LSTM
主要在于 f 函数由LSTM构成。
![Tree LSTM](https://img-blog.csdnimg.cn/20190406101026767.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

## 第六课（Attention based Model）
如下图所示，输入一个数据到中央控制器（DNN/RNN/...）之后中央控制器会根据attention weight 去控制接下来要attention到哪个位置，也就是说会选择相应位置将数据读取出来。
![Attention based Model](https://img-blog.csdnimg.cn/20190406101700223.png)
### 1 Reading Comprehension(阅读理解)
让机器去读一篇文章，并且理解其中的内容。
##### (1) Memory Network
如下图所示，为MN的基础版本，将一个文章中的每个句子和提出的quesion各变为一个vector，计算question和每个句子的相似度α（即匹配度，weight），之后再做weight sum得出结果输入DNN得到最终answer。
![ Memory Network-base](https://img-blog.csdnimg.cn/20190406103358166.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
如下图所示，为MN的进阶版本，其中利用不同的matrix（learn 出来的）将句子进行两次transform（x相当与hash map的key，h相当于hash map的value），利用 h 和 相似度α 计算出weight sum得到一轮的结果，hopping的意思是将一轮计算出的结果输入到下一轮继续参与计算，不断重复（次数可以自定义也可以learn出来）...
![Memory Network](https://img-blog.csdnimg.cn/20190406104141400.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
<strong>Visual Question Answering</strong>
![Visual Question Answering](https://img-blog.csdnimg.cn/20190406115529562.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
<strong>Neural Turing  Machine</strong>
Memory Network的另一个版本，不但可以读数据还可以写数据。但是运用不太广泛，可能是太复杂很难train起来。
![Neural Turing  Machine](https://img-blog.csdnimg.cn/20190406120356326.png)
<strong>Stack RNN</strong>
当前使用的也不是很广泛，下图中的Push, Pop, Nothing为三个动作，不是看哪个概率大就只做哪个动作，而是三个动作都做，只不过相应动作的权重不同。
![Stack RNN](https://img-blog.csdnimg.cn/20190406121428490.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

## 第七课（ForDeep）
此节课程主要讲述有哪些方法可以应对train不起来的情况。
![outline](https://img-blog.csdnimg.cn/20190406121849981.png)
### 1 Batch Normalization
将一个一个Batch的数据都做Normalization。
![Batch-N](https://img-blog.csdnimg.cn/20190406124018380.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
<strong>At test stage</strong>
由于在test阶段，数据都是一笔一笔地输入，所以我们没有办法得到相应的 μ 和 σ ,
理想的解决方法是再用整个train-data计算出整体的 μ 和 σ ，但是在unlive的model中不保存train-data的数据。
实际的解决办法是在 training 时拿每笔batch的 μ 和 σ 在最后求均值做为 test 中的参数。
![Test](https://img-blog.csdnimg.cn/20190406124817438.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
<strong>Benefit:</strong>
![Benefit](https://img-blog.csdnimg.cn/20190406143733624.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

### 2 Activation Function
##### (1) SELU
特性：
![SELU](https://img-blog.csdnimg.cn/20190406150310367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![SELU1](https://img-blog.csdnimg.cn/20190406151937855.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

### 3 Highway Network
将GRU中gate的概念用于Feedforward network。解决了训练深层神经网络的难题，Highway Networks 允许信息高速无阻碍的通过深层神经网络的各层，这样有效的减缓了梯度的问题，使深层神经网络不在仅仅具有浅层神经网络的效果。
![Highway Network](https://img-blog.csdnimg.cn/20190406154421897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 3 Grid LSTM
在time和layer上都存在memory。
![Grid LSTM](https://img-blog.csdnimg.cn/20190406155737330.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

## 第八课（Tuning Hyperparameters）
### 1 grid search(网格搜索)
一种调参手段；穷举搜索：在所有候选的参数选择中，通过循环遍历，尝试每一种可能性，表现最好的参数就是最终的结果。其原理就像是在数组里找最大值。（为什么叫网格搜索？以有两个参数的模型为例，参数a有3种可能，参数b有4种可能，把所有可能性列出来，可以表示成一个3*4的表格，其中每个cell就是一个网格，循环过程就像是在每个网格里遍历、搜索，所以叫grid search）。 
![grid search](https://img-blog.csdnimg.cn/20190406161525222.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)