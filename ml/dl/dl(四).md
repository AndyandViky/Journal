

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
# DL入门（四）powered by @李宏毅

-----------------------------------

## 第十课（DRL概念）
### 1 Proximal Policy Optimization(PPO,近端策略优化)
##### (1) Policy Gradient
![Policy Gradient](https://img-blog.csdnimg.cn/20190424131529736.png)
如上图所示：policy为一个network，输入为一个vector或者matrix，输出为每种行为执行的概率。
![Policy Gradient](https://img-blog.csdnimg.cn/20190424132505188.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
上图为Expected Reward的计算方法。
![Policy Gradient](https://img-blog.csdnimg.cn/20190424133825650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
上图为具体的演算法。
##### (2) From on-policy to off-policy
![on-policy to off-policy](https://img-blog.csdnimg.cn/20190425134106480.png)
on-policy的意思是训练出来的Agent和与环境互动的Agent是同一个，off-policy的意思是训练出来的Agent和与环境互动的Agent不是同一个。

&emsp;<strong>1 Importance Sampling</strong>
&emsp;重要性采样属于众多采样方法中的一种，核心思想是当我们无法在原定的p(x)中进行sample的时候，我们可以找到一个q(x)分布，通过从q(x)中进行采样之后再乘上一定的权重使其分布非常接近p(x)。
&emsp;但是当p(x)和q(x)相差特别大时，在sample次数有限的情况下p(x)和q(x)求出的方差将会相差较大。
![Importance Sampling](https://img-blog.csdnimg.cn/20190425135836385.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
![Importance Sampling](https://img-blog.csdnimg.cn/20190425141020601.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
下图是on-policy向off-policy转换的过程，利用Importance Sampling的思想，训练出来的Agent为p(x)，与环境交互的Agent为q(x)，在q(x)上sample数据最终乘上一定的权值便可以训练p(x)。
![From on-policy to off-policy](https://img-blog.csdnimg.cn/20190425142350257.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
下图为PPO的公式，在上图式子的基础下加上了一个Constrain，使得p(x)，q(x)在最后不会差的太多，其中的KL divergence代表的不是两个参数之间的距离，而是指带入两个参数输出的action之间的不同。
 ![PPO思想](https://img-blog.csdnimg.cn/20190425143013506.png)
![PPO算法](https://img-blog.csdnimg.cn/20190425144242328.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (3) PPO2
下图为PPO2的相关公式。没有计算KL的过程，计算速度更快，更加的simple。 
![PPO2](https://img-blog.csdnimg.cn/20190425145219211.png)
### 2 Q-Learning
![Q-learning](https://img-blog.csdnimg.cn/20190425145525931.png)
##### (1) Basic Idea
&emsp;<strong>1 Critic</strong>
&emsp;Critic的作用在与给定一个State和Actor，去预测最终会得到的Reward。
&emsp;![Q-learning](https://img-blog.csdnimg.cn/2019042515002952.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;<strong>How to estimate V(s) ?</strong>
&emsp;(1) MC-base，使V(s)求出的值和最终episode结束后计算出的reward的值越接近越好。此方法必须计算G，意味着必须玩到游戏结束。
&emsp;![MC-base](https://img-blog.csdnimg.cn/20190425150944614.png)
&emsp;(2) TD-base，前一个V(s)的值等于V(s+1)的值加上前一次得到的Rt。
&emsp;![TD-base](https://img-blog.csdnimg.cn/20190425151540173.png)
&emsp;![MC-vs-TD](https://img-blog.csdnimg.cn/20190425152135383.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;<strong>2 Another Critic</strong>
&emsp;Q function，核心思想是当遇到某个state s时强制操作action π（在正常情况下不一定执行action a），之后再一直train下去。
![Another Critic](https://img-blog.csdnimg.cn/20190425153643572.png)
下图为Q-Learning的一般思想。
![Q-learning](https://img-blog.csdnimg.cn/20190425155558772.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (2) Tips of Q-Learning
&emsp;<strong>1 target network</strong>
&emsp;由于Q这两个neural初始是一样的，在训练时两个会同时update参数，这样会导致最终的参数会是两次update相加的结果，Target Network提供的思想就是先固定住后一个neural的参数，只update前一个neural的参数，在update若干次后再将前一个的参数赋给后面的那个neural，这样就需要重新train，之后再不断往复。
&emsp;![target network](https://img-blog.csdnimg.cn/20190425160024524.png)
&emsp;<strong>2 Exploration</strong>
&emsp;在原来的Q-Learning思想中是选取Q(s,a)最大的那个action，但是会出现只sample某一个action，而其他action都不会被sample，就像去某个餐厅吃饭点了一个菜很好吃，那么之后去这个餐厅都只点这个菜，然后我们就不会知道其他菜好不好吃。所以我们需要有一定的几率去exploration其他action。
&emsp;![Exploration](https://img-blog.csdnimg.cn/20190425160802153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
&emsp;<strong>3 Replay Buffer</strong>
&emsp;核心思想是拿Agent与环境做交互，产出大量的Experience放到一个比较大的buffer里，之后使用迭代训练，每一次迭代都从buffer中随机sample一个batch的数据进行训练。
&emsp;![Replay Buffer](https://img-blog.csdnimg.cn/20190425162827651.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)