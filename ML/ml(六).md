<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
# ml入门（六）powered by @李宏毅

-----------------------------------

## 第十五课（Reinforcement Learning）
让机器根据不同的action将会得到不同的response，可能是不好的，也可能是好的。reinforcement learning要做到的事情就是让reward最大化。
有时reward的情况是非常sparse的，大部分reward的返回都为0。RL的难点在于如何在sparse的情况下得到好的效果。
![Reinforcement Learning](https://img-blog.csdnimg.cn/20190402102752485.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 1 RL vs Supervised
![RLvsSupervised](https://img-blog.csdnimg.cn/20190402103742666.png)
### 2 A3C(Asynchronous Advantage Actor-Critic)
##### (1) Police based
<strong>What is the Actor/Police?</strong>
Actor就是maching learning里的function，其作用为输入observation(state)需要输出相应的Action。
<strong>学习步骤：</strong>
1. 找到一个function set
![RL1](https://img-blog.csdnimg.cn/20190402111133623.png)
2. 懂得评判function的好坏
 ![RLsecond](https://img-blog.csdnimg.cn/20190402114446403.png)
3. 找到最好的那个function
![RL3](https://img-blog.csdnimg.cn/20190402143500944.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (2) Critic
Critic是一个评判Vector好坏的函数，Critic可以和Vector一起tarin，这样效果比较好。
![Critic](https://img-blog.csdnimg.cn/20190402144418274.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)