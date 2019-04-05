<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
# DL入门（一）powered by @李宏毅

-----------------------------------

## 第一课（Deep Learning概览）
<strong>outline:</strong>
![DL概览](https://img-blog.csdnimg.cn/20190402153643893.png)
### 1 Universality 
theroy: 使用一层的Neural network就可以fit所有的函数。
![Universality ](https://img-blog.csdnimg.cn/20190402165628233.png)
<strong>what is the L-Lipschitz function?</strong>
output的变化一定要小于等于input的变化。
![L-Lipschitz](https://img-blog.csdnimg.cn/20190402170021324.png)
<strong>how many neurons are needed to approximate f*?</strong>
![U-nuerons](https://img-blog.csdnimg.cn/20190402171849468.png)
找到一个K，即K个nuerons使得f(x)到目标函数f*(x)的最大值不超过一个Σ。
![U-N(k)](https://img-blog.csdnimg.cn/20190402173714985.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
所有的function都是分段函数，所以要找到这样的函数就需要在f*上点出若干个点然后将其连接，只需要将f*和两点间线段的距离缩小至小于等于Σ。

<strong>如何使用两个neural表示一个线段?</strong>
由限制条件可知，分段总共可以由L/Σ个，将每个分段表示为下图的形式。
![分段函数的不同表示](https://img-blog.csdnimg.cn/20190403092608943.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
利用两个relu就可以表示其中一条线段，即relu1 + relu*(-1)。
![2relu](https://img-blog.csdnimg.cn/20190403091808355.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

### 2 为什么要用Deep？
##### (1) relu的upper bound
因为relu的式子为max(0, x)，即只有两种可能的输出，当有N个Neural时，最多有2^N个linear patten。
![relu-upper-bound](https://img-blog.csdnimg.cn/20190403094045186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (2) deep vs shallow
由下图可知增加深度比只增加宽度更有效。
![deep vs shallow](https://img-blog.csdnimg.cn/20190403101033554.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
由下图可知，deep中的前几层非常重要，就像折纸一样，前面几层往往是决定性的。
其中紫色为第一层，依次往上。可以看出改变第一层参数时正确率急剧下降，但是改变最后一层（红色）的参数时，结果几乎没什么变化。
![deep中的前几层非常重要](https://img-blog.csdnimg.cn/20190403101308449.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (2) Using deep structure to fit functions
&emsp;<strong>1 for example to fit f(x)=x^2</strong>
![f(x)=x^2](https://img-blog.csdnimg.cn/20190403102936330.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;f1(x) 可由第一个图形减去第二个图形获得，f2(x) 可由第一个图形减去第二个图形再减去第三个图形获得，f3(x) ......
![fm(X)](https://img-blog.csdnimg.cn/20190403103414672.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;使用Deep得到fm(x)只需要m个neurons。其中的激活函数为由relu制成的取（绝对值）函数。

&emsp;<strong>2 f(x)=x^2的演化，multiply Net</strong>
![演化1](https://img-blog.csdnimg.cn/20190403104236819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;<strong>3 f(x)=x^2和multiply Net的演化，Polynomial(多项式)</strong>
![演化2](https://img-blog.csdnimg.cn/20190403104514259.png)

## 第二课（Optimization）
Deep learning 中的loss function 不是 convex的！！！
<strong>outline:</strong>
![大纲](https://img-blog.csdnimg.cn/20190403112430316.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 1 Hessian
黑塞矩阵（Hessian Matrix），又译作海森矩阵、海瑟矩阵、海塞矩阵等，是一个多元函数的二阶偏导数构成的方阵，描述了函数的局部曲率。利用黑塞矩阵可判定多元函数的极值问题，在工程实际问题的优化设计中，所列的目标函数往往很复杂，为了使问题简化，常常将目标函数在某点邻域展开成泰勒多项式来逼近原函数，此时函数在某点泰勒展开式的矩阵形式中会涉及到黑塞矩阵。
此处引入Hessian matrix的原因是，它的特性可以看出当前点是local minima,global minima还是saddle point！！！
##### (1) when gradient is zero
&emsp;<strong>在什么情况下gradient为0？</strong>
1. 当到达local minima时
2. 当到达global minima时
3. 当到达一个saddle point时（鞍点）
##### (2) 什么是Hessian matrix?
&emsp;![Hessian](https://img-blog.csdnimg.cn/20190403131017477.png)
&emsp;Hessian matrix就是对各个元素求二阶偏导组成的矩阵。
##### (3) 牛顿法
&emsp;![牛顿法](https://img-blog.csdnimg.cn/20190403132409883.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;![牛顿法1](https://img-blog.csdnimg.cn/20190403133239989.png)
&emsp;牛顿法可以做到一步直接到当前曲线的最小值，再找到当前曲线最小值对应于原曲线的x，在x点继续使用牛顿法计算，一直这样下去直到找到gradient为0的地方。牛顿法每次走一大步，gradient descent每次走一下步。
&emsp;牛顿法在Deep Learning中用处不大！！！首先：H矩阵太大，对其求逆几乎不可能，然后：牛顿法不能保证找到global minima，它也有可能找到gradient为0的saddle point。
##### (3) 正定矩阵性质复习
![正定 ](https://img-blog.csdnimg.cn/20190403134454333.png)
##### (4) 如何使用H去辨别当前的critical point
![如何使用H去辨别当前的critical point](https://img-blog.csdnimg.cn/20190403143816133.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (5) Degenerate
Degenerate Hessian has at least one zero eigen value.
在特征为0的这点我们求不出crtical point的类别。

## 第三课（Deep Network）
### 1 Deep Linear Network
没有local minima,所有走到的local minima 都是 global minima。
### 2 Conjecture(推论)
几乎所有的local minimum的损失于global minimum的损失是差不多的！！！
![分析hessian](https://img-blog.csdnimg.cn/20190403163350921.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
由图得出结论，当参数非常多的时候，当前critical point是local minimum的几率几乎没有。
![特征值的出现](https://img-blog.csdnimg.cn/20190403163840389.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![guess about errpr surface](https://img-blog.csdnimg.cn/20190403164107744.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
由图可得出结论，saddle point比较容易出现在loss在的地方，local minimum比较容易出现在loss低的地方。
### 3 BatchNorm(batch normalization)
<strong>“Internal Covariate Shift”问题</strong>
对于深度学习这种包含很多隐层的网络结构，在训练过程中，因为各层参数不停在变化，所以每个隐层都会面临covariate shift的问题，也就是在训练过程中，隐层的输入分布老是变来变去，这就是所谓的“Internal Covariate Shift”，Internal指的是深层网络的隐层，是发生在网络内部的事情，而不是covariate shift问题只发生在输入层。

<strong>基本思想</strong>：因为深层神经网络在做非线性变换前的激活输入值（就是那个x=WU+B，U是输入）随着网络深度加深或者在训练过程中，其分布逐渐发生偏移或者变动，之所以训练收敛慢，一般是整体分布逐渐往非线性函数的取值区间的上下限两端靠近（对于Sigmoid函数来说，意味着激活输入值WU+B是大的负值或正值），所以这导致反向传播时低层神经网络的梯度消失，这是训练深层神经网络收敛越来越慢的本质原因，而BN就是通过一定的规范化手段，把每层神经网络任意神经元这个输入值的分布强行拉回到均值为0方差为1的标准正态分布，其实就是把越来越偏的分布强制拉回比较标准的分布，这样使得激活输入值落在非线性函数对输入比较敏感的区域，这样输入的小变化就会导致损失函数较大的变化，意思是这样让梯度变大，避免梯度消失问题产生，而且梯度变大意味着学习收敛速度快，能大大加快训练速度。
![BN](https://img-blog.csdnimg.cn/20190405095826649.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 4 Generalization(范化)
![范化](https://img-blog.csdnimg.cn/20190405101042818.png)
由上图可知，test-data 的 Error 的 upper-bound 为 train-data 的 Error + Ω（R, M, δ）
R 为训练数据的数量，M为model的容积。
<strong>如何得到model的容积（VC dimension）？</strong>
![VC-dimension](https://img-blog.csdnimg.cn/20190405101935997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
如图所示，有两张图片为鹿，一张为马，给三个图片随机分配label，总共有8中可能，如果在所有的可能情况下maching都能得到0%的error。那么就称其VC dimension >= 3。
##### (1) indicator of generalization
![indicator](https://img-blog.csdnimg.cn/20190405104711151.png)
##### (2) Sensitivity(敏感度)
&emsp;用于测定是否overfitting。值越高越容易是错误的。sensitivity和generalization成正比，意味着在得到一笔没有label的test-data时可以先用sensitivity预测效果。
&emsp;<strong>1 Jacobian matrix</strong>
&emsp;雅可比矩阵的重要性在于它体现了一个可微方程与给出点的最优线性逼近
&emsp;![jacobian](https://img-blog.csdnimg.cn/20190405111505297.png)
&emsp;用于测定的sensitivity就是jacobian matrix的Forbenius norm,Forbenius norm就是每个元素的平方和再开根号。
&emsp;![Sensitivity](https://img-blog.csdnimg.cn/20190405112638559.png)
##### (3) Sharpness(敏感度)
ps: 实验结果得出使用small batch size进行训练会得到较为平坦的sharpness。
![sharp-minima](https://img-blog.csdnimg.cn/20190405123108625.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
由上图看出，如果在sharp找到minima，那么这个model在test上过拟合的概率更大。
<strong>1 sharpness的定义</strong>
![sharpness的定义](https://img-blog.csdnimg.cn/20190405123608430.png)
第一种是在谷底往上一个ξ，然后计算在ξ平面上山体的面积（二维平面），平面面积越大越平坦，或者ξ两边点之间的线段长度（一维）...
第二种是以θ为圆心固定半径画一个圆，找到圆中loss最大的点然后减去θ点的loss作为sharpness。

##### (4) 额外知识：Computational graph
![Computational graph](https://img-blog.csdnimg.cn/20190405165013556.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
