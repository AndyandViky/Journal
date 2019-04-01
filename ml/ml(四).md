<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
# ml入门（四）powered by @李宏毅

-----------------------------------

## 第十课（迁移学习）
<strong>迁移学习要解决的问题是</strong>：假设现在手上有与task不直接相关的data，这些data能否帮助我们完成task呢？
比如现在要做猫狗的分类器，所谓不直接相关的data就可能是  
- Similar domain, different tasks（如，大象与老虎的图片，与猫狗图片的分布是相像的） 
- Different domain, same task（如，猫狗的卡通图片，与猫狗图片的分布是不像的）

<strong>迁移学习分类：</strong>
![分类](https://img-blog.csdnimg.cn/2019040109565045.png)
### 1 model fine-tuning
利用source data训练出模型后再使用target data微调模型。可能存在的问题是，由于target data可能会很少，所以就算用source data训练出的模型很好，再用target data做fine-tune的时候可能会过拟合。  
![model fine-tuning](https://img-blog.csdnimg.cn/20190329222810792.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (1) 由于target data较少，容易过拟合，解决办法：
&emsp;<strong>1 conservative training(保守训练)</strong>  
&emsp;copy大部分参数，update小部分参数，或者直接copy部分layer，然后用target-data训练剩下的layer。
![conservative training](https://img-blog.csdnimg.cn/20190329222920882.png)
&emsp;_which layer can be transferred(copied)?_
![layer](https://img-blog.csdnimg.cn/20190329222935355.png)
### 2 multitask learning(多任务学习)
此种方法在同时训练多种语言识别的效果很好，性能较高。  
Multitask Learning 与 Fine-tuning 的区别在于， Fine-tuning 在意的是在target domain上做得好不好，不介意fine-tune 之后在source domain 上结果坏掉。而在Multitask Learning 中，要同时在意在target domain与source domain上做得好不好
![多任务](https://img-blog.csdnimg.cn/20190329231440778.png)
### 3 Progressive Neural Network
如果两个task很不相关，却又做了transfer，效果可能是负面的。有人提出了Progressive Neural Network，使得两个task即使无关，做transfer效果也不会比不做更差。 
![progressive nn](https://img-blog.csdnimg.cn/20190401100411931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
训练好第一个模型后，第一个模型的参数就fix住。训练第n个模型时，每个隐层都借用前n-1个模型的隐层输出。
### 4 Domain-adversarial training(领域对抗性训练)
可以看为GAN的一种， 目的在于将resourse data和target data转到同样的domain，让它们有接近的分布。  
![domain1](https://img-blog.csdnimg.cn/20190329231347270.png)
![domain2](https://img-blog.csdnimg.cn/20190329231400421.png)
### 5 zero-shot learning
适合Target Data: unlabelled, Source Data: labelled的情况，将source data视作training data，将target data视作testing data。但二者是不同的task。
![zero-shot](https://img-blog.csdnimg.cn/2019032923233175.png)
![zero-shot1](https://img-blog.csdnimg.cn/20190329233852679.png)
![zero-shot2](https://img-blog.csdnimg.cn/20190329233901543.png)
上面第一行的目标函数是有问题的，因为它只考虑了同一个pair在embedding之后尽可能接近，没有考虑不同pair在embedding之后尽可能拉开，所以可能会把所有的xn,yn 都投到同一点。应改成图中第二行的目标函数。
## 第十一课（svm）
SVM 求解使通过建立二次规划原始问题，引入拉格朗日乘子法，然后转换成对偶的形式去求解，这是一种理论非常充实的解法。
![svm](https://img-blog.csdnimg.cn/20190329234953666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 1 hinge loss
![hinge-loss](https://img-blog.csdnimg.cn/20190330093102602.png)
### 2 linear svm
![linear-svm](https://img-blog.csdnimg.cn/20190330095720147.png)
Linear SVM的function是linear的，如图中公式所示，当f(x)>0f(x)>0 时，x属于class 1, 当f(x)<0f(x)<0 时，x属于class 2。 
Linear SVM的loss function是hinge loss + 正则项，由于hinge loss和正则项都是convex的，所以loss function整体也是convex的。曲线有棱棱角角也是可微分的。 
Linear SVM与Logistic regression 区别只在于损失函数不同，前者的损失函数是hinge loss，后者的损失函数是cross entropy。 
##### (1) 核函数
核函数的意义在于映射后高维特征空间的内积可以通过原来低维的特征得到。
<strong>Dual Representation</strong>
Dual Representation指的是：最小化损失函数的权重参数w∗ 可以表示为数据点xn 的线性组合。一般用拉格朗日乘子法解释这一结论。
![svm1](https://img-blog.csdnimg.cn/20190330102022118.png)
![核函数](https://img-blog.csdnimg.cn/20190330102911770.png)
&emsp;<strong>1 rbf kernel(radial basis function)</strong>
&emsp;![rbf-kernel](https://img-blog.csdnimg.cn/20190330104744626.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;RBF Kernel, x与z越像，则K(x,z) 越大。它是两个无穷维特征向量的内积。将核函数展开并使用泰勒级数，可见核函数是无穷项之和，每项都可写成内积形式，将与x,z有关的向量分别串起来，得到两个无穷维的向量，这两个向量的内积就是RBF核函数。
&emsp;ps: 容易为无穷长的内积，所以容易产生过拟合
&emsp;<strong>2 sigmoid kernel</strong>
&emsp;sigmoid kernel可看做一个单隐层网络，neuron个数就是支持向量个数，neuron权重就是支持向量各维度的数值。
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
&emsp;用最大特征得分减去正确的特征得分。
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
由于训练数据和测试数据有mismatch的问题，所以加上正则项让w 接近0 的话会鲁棒一些。 
加上正则项之后，参数 w 的更新类似于DNN中的weight decay. 
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
![sequence labeling](https://img-blog.csdnimg.cn/2019033115504577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (1) Hidden Markov Model(HMM)
HMM所做的事情就是描述一句话是如何说出来的。
HMM假设经过如下两个步骤产生句子：根据语法，产生词性序列；根据词性序列和词典，产生句子。 
语法表现为马尔科夫链的形式，可得产生词性序列的概率。从各词性集合中采样得到句子，相应的得到在给定词性序列的条件下，产生句子的条件概率。
![HMM](https://img-blog.csdnimg.cn/2019033115592528.png)
![HMM1](https://img-blog.csdnimg.cn/20190331160209814.png)
&emsp;<strong>1 How you generate a sentence?</strong>
&emsp;![generate a sentence](https://img-blog.csdnimg.cn/20190331155025713.png)
&emsp;<strong>2 viterbi algorithm</strong>
&emsp;穷举所有可能的y太过复杂，可以使用viterbi算法。告诉算法p(x, y)如何计算，viterbi算法就会返回y使得p(x, y)最大。
&emsp;![viterbi algorithm](https://img-blog.csdnimg.cn/20190331161257274.png)
##### (2) Condition Random Field(CRF)
![CRF](https://img-blog.csdnimg.cn/20190331163657622.png)
![CRF1](https://img-blog.csdnimg.cn/20190331164807943.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![CRF2](https://img-blog.csdnimg.cn/20190331170115972.png)
小结：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190401092100518.png)
&emsp;<strong>1 gradient ascent</strong>
&emsp;![gradient-ascent](https://img-blog.csdnimg.cn/20190331171606417.png)
&emsp;<strong>2 CRF vs HMM</strong>
&emsp;![CRF vs HMM](https://img-blog.csdnimg.cn/20190401092006396.png)

&emsp;总结：
&emsp;![s-l-p总结](https://img-blog.csdnimg.cn/20190401093656776.png)