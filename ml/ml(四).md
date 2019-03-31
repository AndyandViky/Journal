<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
# ml入门（四）powered by @李宏毅

-----------------------------------

## 第十课（迁移学习）
### 1 model fine-tuning
![model fine-tuning](https://img-blog.csdnimg.cn/20190329222810792.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (1) 由于target data较少，容易过拟合，解决办法：
&emsp;<strong>1 conservative training(保守训练)</strong>  
&emsp;copy大部分参数，update小部分参数  
![conservative training](https://img-blog.csdnimg.cn/20190329222920882.png)
&emsp;_which layer can be transferred(copied)?_
![layer](https://img-blog.csdnimg.cn/20190329222935355.png)
### 2 multitask learning(多任务学习)
![多任务](https://img-blog.csdnimg.cn/20190329231440778.png)
### 3 Domain-adversarial training(领域对抗性训练)
可以看为GAN的一种， 目的在于将resourse data和target data转到同样的domain，让它们有接近的分布。  
![domain1](https://img-blog.csdnimg.cn/20190329231347270.png)
![domain2](https://img-blog.csdnimg.cn/20190329231400421.png)
### 4 zero-shot learning
![zero-shot](https://img-blog.csdnimg.cn/2019032923233175.png)
![zero-shot1](https://img-blog.csdnimg.cn/20190329233852679.png)
![zero-shot2](https://img-blog.csdnimg.cn/20190329233901543.png)

## 第十一课（svm）
![svm](https://img-blog.csdnimg.cn/20190329234953666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 1 hinge loss
![hinge-loss](https://img-blog.csdnimg.cn/20190330093102602.png)
### 2 linear svm
![linear-svm](https://img-blog.csdnimg.cn/20190330095720147.png)
![svm1](https://img-blog.csdnimg.cn/20190330102022118.png)
##### (1) 核函数
![核函数](https://img-blog.csdnimg.cn/20190330102911770.png)
&emsp;<strong>1 rbf kernel(radial basis function)</strong>
&emsp;![rbf-kernel](https://img-blog.csdnimg.cn/20190330104744626.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;ps: 容易为无穷长的内积，所以容易产生过拟合
&emsp;<strong>2 sigmoid kernel</strong>
&emsp;![sigmoid-kernel](https://img-blog.csdnimg.cn/20190330105232156.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

## 第十二课（structured learning）
### 1 structured linear model
![概览](https://img-blog.csdnimg.cn/2019033015105335.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
有时我们希望输入输出是带有结构的，比如sequence, list, tree, bounding box…… ，就需要用到structured learning。
![框架](https://img-blog.csdnimg.cn/20190330151358730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
总体来说就是找一个函数F:X×Y→R, X,Y是 input structured objects, R 是 real number, 衡量 x,y 有多匹配。 
测试时，对给定的x，穷举所有的y，找令F 最大的ỹ . 
##### (1) 三个问题
![3个问题](https://img-blog.csdnimg.cn/20190330130222398.png)
![问题1](https://img-blog.csdnimg.cn/20190330154721338.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![问题2](https://img-blog.csdnimg.cn/20190330154738924.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![问题3](https://img-blog.csdnimg.cn/20190330155012607.png)
##### (2) 总结
![总结](https://img-blog.csdnimg.cn/20190330153331468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 2 structured svm
![钢要](https://img-blog.csdnimg.cn/20190330161028203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (1) separable
能够找出一个w使得正确的point比其同样形状的point值都至少大一个δ
![separable](https://img-blog.csdnimg.cn/20190331092536846.png)
##### （2) non-separable
没有办法使得正确答案的分数高于其他答案的分数
![non-separable](https://img-blog.csdnimg.cn/20190331102627355.png)
&emsp;<strong>1 loss function</strong>
&emsp;![loss](https://img-blog.csdnimg.cn/20190331104027334.png)
&emsp;<strong>2 将损失最小化（GD）</strong>
&emsp;![GD](https://img-blog.csdnimg.cn/20190331104040154.png)
##### （3) considering errors
考虑多个不同的结果可能相差不大，例如两个结果都成功圈出了目标，但是圈出的范围略有不同，这种情况下如果使用分数较差那个结果也是可以的。这就需要求出多个不同结果之间的差异分数。
&emsp;<strong>1 define error function</strong>
&emsp;![error function](https://img-blog.csdnimg.cn/20190331105331686.png)
&emsp;<strong>2 improve loss function</strong>
&emsp;![new-loss-function](https://img-blog.csdnimg.cn/20190331105710788.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### （4) regularization
![sl-regularization](https://img-blog.csdnimg.cn/20190331111211127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### （5) structured svm
![sl-svm](https://img-blog.csdnimg.cn/20190331140913843.png)
##### （6) cutting plane algorithm for structured svm
![cutting-plane](https://img-blog.csdnimg.cn/20190331141623484.png)
&emsp;<strong>1 how to find the A?</strong>
&emsp;&emsp;1. 初始化A=null
&emsp;&emsp;2. 求出最小点w
&emsp;&emsp;3. 查找当前是否有不满足的约束，找到约束最大的那个加入A
&emsp;&emsp;4. 循环2到3，直到找到满足约束的最小w
&emsp;<strong>2 how to find the most violated?</strong>
&emsp;![most -violate](https://img-blog.csdnimg.cn/20190331144116665.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;由于Σ和w*ϕ(x, ỹ)是固定值，所以并不影响计算degree的值
##### （7) multi-class and binary svm
&emsp;<strong>1 multi-class</strong>
&emsp;![multi-svm-class](https://img-blog.csdnimg.cn/20190331152431337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;<strong>2 binary-class</strong>
&emsp;![binary-class](https://img-blog.csdnimg.cn/20190331152829722.png)
##### （8) beyond structured svm
structured svm是linear的，所以做不出来一些很酷炫的东西！
我们可以在提取feature时使用DNN，再接上structured svm！！
![b-structured-svm](https://img-blog.csdnimg.cn/20190331153401719.png)
### 3 sequence labeling problem