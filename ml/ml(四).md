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


