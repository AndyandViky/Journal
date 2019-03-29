
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