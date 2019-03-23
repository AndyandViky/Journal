# ml入门（一）powered by @李宏毅

-----------------------------------
## 第一课（机器学习总揽）
1. ml基本步骤
   1. 找出一个function set
   2. 让maching拥有衡量一个function好坏的能力
      1. loss function L:
          > input: a function output: how bad it is
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
      w* = argmin L(w)
      # consider loss function L(w) with one parameter w:
      first: (Randomly) pick an initial value w0
      second: compute dL/dw|w=w0 (求导)
      third: if the value is negative w1 = w0*-r(dL/dw|w=w0) 其中r称为学习比率
      four: loop second to third
      ```