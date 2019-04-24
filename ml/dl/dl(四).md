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
 

