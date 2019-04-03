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