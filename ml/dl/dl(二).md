<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
# DL入门（二）powered by @李宏毅

-----------------------------------

## 第四课（Sequence to Sequence）
![outline](https://img-blog.csdnimg.cn/20190405180654676.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 1 Sequence Generation
![Sequence Generation](https://img-blog.csdnimg.cn/20190405222622709.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
输入一个vector,输出也为一个vector。
### 2 Conditional Sequence Generation
产生符合某种情景的句子或者图片...
![Conditional Sequence Generation](https://img-blog.csdnimg.cn/20190405224026372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 3 Dynamic Conditional Sequence Generation
假如输入的是一篇非常长的文章，我们没有办法将一个非常大的vector多次输入到Decoder中。
![Dynamic Conditional Sequence Generation](https://img-blog.csdnimg.cn/20190405230028250.png)
<strong>example:</strong>
![Dynamic Conditional Sequence Generation1](https://img-blog.csdnimg.cn/20190405230345901.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### 4 Tips of Generation
1. attention
在一个视频中存在许多的图片，那么machind可能会给某张图片中非常多的attention，例如下图，过于关注图二可能会产生多个woman，我们可以平均化每张图片的attention来试着解决这个问题。
![attention](https://img-blog.csdnimg.cn/20190405232608147.png)
2. exposure bias
如下图，在训练时maching可能只专门训练了某些‘路径’，在训练时maching可能会在第一步就选错，那么无论怎么样后续都不会正确。
![exposure bias](https://img-blog.csdnimg.cn/20190405234042859.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)