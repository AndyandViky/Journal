
# numpy基础操作
==================================================================
numpy（Numerical Python）提供了python对多维数组对象的支持：ndarray，具有矢量运算能力，快速、节省空间。numpy支持高级大量的维度数组与矩阵运算，此外也针对数组运算提供大量的数学函数库。
## (1) 创建数组的简单使用
import numpy as np
创建初始值为0的数组
```
>>> a = np.zeros((2, 3))
>>> a
array([[ 0.,  0.,  0.],
       [ 0.,  0.,  0.]])
```
创建初始值为1的数组
```
>>> a = np.ones((2, 3))
>>> a
array([[ 1.,  1.,  1.],
       [ 1.,  1.,  1.]])
```
自定义初始值数组
```
>>> a = np.full((2, 3), 1)
>>> a
array([[1, 1, 1],
       [1, 1, 1]])
```
创建空的数组
```
>>> a = np.empty((2, 3))
>>> a
array([[ -2.00000000e+000,  -2.00000000e+000,   2.12840685e-314],
       [  6.93867031e-310,   6.93867031e-310,  -2.00000000e+000]])
```
直接定义数组
```
>>> a = np.array([1, 2, 3])
>>> a
array([1, 2, 3])
```
查看数组维度
```
>>> a.shape
(3,)
```
修改数组维度  -1代表占位符， numpy会自动根据数组维度填充
```
>>> a = np.array([[1, 2, 3],
             [2, 3, 4]])
>>> a.shape
(2, 3)
>>> a = a.reshape((1, -1))
>>> a
array([[1, 2, 3, 2, 3, 4]])
```
建立一个单位矩阵
```
>>> a = np.eye(3)
>>> a
array([[ 1.,  0.,  0.],
       [ 0.,  1.,  0.],
       [ 0.,  0.,  1.]])
```
创建0到1随机数的数组
```
>>> a = np.random.random((3, 4))
>>> a
array([[ 0.69618357,  0.18142677,  0.39221575,  0.75385024],
       [ 0.11044473,  0.11558231,  0.43917542,  0.68667204],
       [ 0.24562308,  0.46401773,  0.83062539,  0.77374122]])
```
使用astype转换类型
```
>>> a = numpy.array([1, 2.6, 3],dtype = numpy.float64)
>>> b = a.astype(numpy.int32)
[1 2 3]
```
## (2) 索引操作
可迭代对象的索引，详情参见 _https://blog.csdn.net/AndyViky/article/details/90112511_
numpy中arange的使用。
生成 (3， 5） 范围内的数
```
>>> a = np.arange(3, 5)
>>> print(a)
[3 4]
```
查找大于3的数
```
>>> print(a[a > 3])
[4]
```
布尔型索引
```
x = numpy.array([3,2,3,1,3,0])
# 布尔型数组的长度必须跟被索引的轴长度一致
y = numpy.array([True,False,True,False,True,False]) 
print x[y] # [3,3,3] 
print x[y==False] # [2,1,0]
print x>=3 # [ True False  True False  True  False]
print x[~(x>=3)] # [2,1,0]
print (x==2)|(x==1) # [False  True False  True False False]
print x[(x==2)|(x==1)] # [2 1]
x[(x==2)|(x==1)] = 0
print x # [3 0 3 0 3 0]
```
花式索引
```
x = numpy.array([1,2,3,4,5,6])
print x[[0,1,2]] # [1 2 3]
print x[[-1,-2,-3]] # [6,5,4]
x = numpy.array([[1,2],[3,4],[5,6]])
print x[[0,1]] # [[1,2],[3,4]]
print x[[0,1],[0,1]] # [1,4] 打印x[0][0]和x[1][1]
print x[[0,1]][:,[0,1]] # 打印01行的01列 [[1,2],[3,4]]
# 使用numpy.ix_()函数增强可读性
print x[numpy.ix_([0,1],[0,1])] #同上 打印01行的01列 [[1,2],[3,4]]
x[[0,1],[0,1]] = [0,0]
print x # [[0,2],[3,0],[5,6]]
```
## (3) 元素的数据类型
python会根据数值自动判断类型
查看当前元素类型
```
>>> a = np.array([1, 2, 3])
>>> a.dtype
dtype('int64')
```
强制定义类型
```
>>> a = np.array([22.2, 213], dtype=np.int64)
>>> a
array([ 22, 213])
```
## (4) 数组运算与常用函数
下图为一元函数图
![一元函数](https://img-blog.csdnimg.cn/20190512164605267.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
下图为二元函数图
![二元函数图](https://img-blog.csdnimg.cn/2019051216462186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
下图为常用统计函数
![常用统计函数](https://img-blog.csdnimg.cn/20190512164844810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
下图为去重以及集合操作
![去重以及集合操作](https://img-blog.csdnimg.cn/20190512165212318.png)
下图为线性代数常用操作
![线性代数常用操作](https://img-blog.csdnimg.cn/20190512165247451.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
下图为随机数生成函数
![随机数生成](https://img-blog.csdnimg.cn/20190512165423633.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
numpy支持矩阵的进行数值的加减乘除。
```
>>> a = np.array([[1, 2],
          [2, 3]])
>>> b = np.array([[2, 3],
            [2, 2]])
>>> a+b
array([[3, 5],
       [4, 5]])
>>> a-b
array([[-1, -1],
       [ 0,  1]])
>>> a*b
array([[2, 6],
       [4, 6]])
>>> a/b
array([[0, 0],
       [1, 1]])
```
加根号
```
>>> np.sqrt(a)
array([[ 1.        ,  1.41421356],
       [ 1.41421356,  1.73205081]])
```
矩阵乘法
```
>>> a.dot(b)
array([[ 6,  7],
       [10, 12]])
```
计算总数
```
>>> np.sum(a)
8
```
按照列计算总数
```
>>> np.sum(a, axis=0)
array([3, 5])
```
按照行计算总数
```
>>> np.sum(a, axis=1)
array([3, 5])
```
求平均值， 和sum一样， 可以根据行或者列求值
```
>>> np.mean(a)
2.0
```
uniform函数
生成2到5之前的一个随机数
```
>>> np.random.uniform(2, 5)
3.129391942119641
```
tile函数
```
>>> a = np.array([[1, 2],
          [2, 3]])
>>> np.tile(a, (1, 2)) # （1， 2）表示行不变，列按照原来的列横向扩充两列
array([[1, 2, 1, 2],
       [2, 3, 2, 3]])
```
argsort函数
返回排序之后数组元素的下标， 和sum一样， 可以根据行或者列排序 a.argsort(axis=0)
```
>>> a = np.array([[1, 2],
          [2, 3]])
>>> np.argsort(a)
array([[0, 1],
       [0, 1]])
```
矩阵专置
```
>>> a = np.array([[1, 1],
          [2, 6]])
>>> a.T # 或者使用 np.transpose(a)
array([[1, 2],
       [1, 6]])
```

ps：图片来源于 _https://blog.csdn.net/cxmscb/article/details/54583415_
##### 该博文仅作学习使用。