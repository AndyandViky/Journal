<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
# DL入门（三）powered by @李宏毅

-----------------------------------

## 第九课（GAN详解）
![GAN](https://img-blog.csdnimg.cn/20190406212906709.png)
### 1 Basic Idea of GAN
##### (1) Generator
generator就是一个neural network或者一个function，输入一个vector，输出一个vector。
![Generator](https://img-blog.csdnimg.cn/2019040621314569.png)
##### (2) Discriminator
discriminator就是一个neural network或者一个function，输入为一张图片或者一个句子...
输出为一个scalar，scalar越大表示越真实。
![Discriminator](https://img-blog.csdnimg.cn/2019040621365838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (3) 总览
Generator不接触真实数据，Discriminator接触真实数据，第一代的G和D非常弱，可以说是随机生成的，接下来G1开始进化为G2使得D1检测G2的效果下降，D1根据G2同样进行相应的进化（训练）直到对G2达到较好的效果，接下来G和D在不断竞争中进化。
![GAN](https://img-blog.csdnimg.cn/20190406215352857.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
##### (4) 算法步骤
第一步：初始化Generator和Discriminator
第二步：固定住Generator的参数，update Discriminator的参数
第三步：固定住Discriminator的参数，update Generator的参数
第四步：迭代第二步至第三步
![步骤1](https://img-blog.csdnimg.cn/20190406220712407.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
具体步骤：
图中公式表示为：
Learning D：最大化sample出来的真实的m张图片的分数，最小化G产生出来的m张图片的分数。
Learning G：最大化G生成的图片在D中的分数，意味着G必须调整自己的参数使得生成的图片越来越接近真实的图片。
![步骤2](https://img-blog.csdnimg.cn/20190406220725999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)

### 2 GAN as Structured learning