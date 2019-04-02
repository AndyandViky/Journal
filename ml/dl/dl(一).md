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
