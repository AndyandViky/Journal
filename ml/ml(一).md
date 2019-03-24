
# ml入门（一）powered by @李宏毅

-----------------------------------
![learning-map](https://img-blog.csdnimg.cn/20190324144204972.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
## 第一课（机器学习总揽）
1. ml基本步骤
   1. 找出一个function set
   2. 让maching拥有衡量一个function好坏的能力
      1. loss function L:
          > input: a function output: how bad it is
          ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190324151349742.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
   3. 使用一个好的演算法挑出最好的那个function
      > find a function with min loss  
      > f* = argmin L(f)
2. ml category
   1. supervised learning // learning from teacher
      1. regression
          > the output of the target function f is 'scalar'
      2. classification
          1. binary classification
              > the output of the target function f is 'Yes or No'
          2. multi-class classification
              > the output of the target function f is one of the 'class1, class2, .... classN'
      3. structured learning
          > the output of the target function f is a structural output
   2. semi-supervised learning
   3. transfer learning
   4. unsupervised learning
   5. reinforcement learning // learning from critics

## 第二课（regression）
1. gradient descent
    1. for example
        ```
        linear model y = w*x + b; w为权值，b为偏差， 目标是利用梯度下降求得最佳参数w和b;
        // ps: 所谓线性模型是指，函数的参数对于输出来说是否为线性;
        w*, b* = argmin L(w, b)
        # consider loss function L(w, b) with two parameter w, b:
        first: (Randomly) pick an initial value w0, b0
        second: 分别为w，b在w=w0，b=b0得情况下求偏导
        third: if the value is negative w1 = w0-r(@L/@w|w=w0,b=b0)， b1 = b0-r(@L/@b|w=w0,b=b0)其中r称为学习比率（@暂且代表求偏导符号）
        four: loop second to third
        five: get average error 每个点到所求出的损失函数的距离之和的平均值
        last: get best w, b
        // ps: 此处的best不一定代表的为global min, 有可能在某一点偏导数都为0但却不是global min。
        ```
    2. learning rate
        > rate太大loss可能会快速增大，rate太小可能收敛速度很慢
        1. rate选取的解决办法
            1. 使用动态rate，每一次迭代都根据固定公式减小rate， 因为越到后面rate应该越小，精确度才会更高
            2. 不同的参数给予不同的learning rate          
        2. 计算rate的方法
            1. adagrad
                > 每次计算所有example的loss, 之后更新参数
                ![learning-rate](https://img-blog.csdnimg.cn/2019032415122315.png)
                ![子公式](https://img-blog.csdnimg.cn/20190324153431626.png)
                ![详细公式](https://img-blog.csdnimg.cn/20190324152204316.png) 
            1. stochastic gradient descent
                > 每次只考虑一个example，算完一个example的loss就更新一次参数
                ![stochastic](https://img-blog.csdnimg.cn/20190324155614359.png)
    3. feature scaling
        > 由于各个特征的输入值不同，单位不同，导致每个特征参数的改变对loss的影响都不同，所以需要  
        > 将输入值进行标准化，使得所有特征参数的改变对loss的影响相同或者相差不大。
2. overfitting(过拟合)
    > ps: overfitting是由于variance过大造成的，bias过大造成underfitting
3. regularization(目的在于寻找更加平滑的函数)

## 第三课（error）
> 训练之后的使用L()求出error来自于biase和variance,知道错误的来源往往可以准确地improve model  
> ps: model越复杂biase越小，variance越大。model越简单biase越大，variance越小
1. 如何确定error来源
   1. 如果模型不能fit训练数据，代表bias大
      > 可能需要去重新设计model(考虑更多的feature或者让model更加复杂)
      ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190324151508847.png)
   1. 如果模型较好地fit了训练数据，但是在测试数据上错误很大，代表variance很大
      > 可以加大训练数据量(非常有效但是并不是每一次都有用)
      > regularization
      ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190324151524756.png)