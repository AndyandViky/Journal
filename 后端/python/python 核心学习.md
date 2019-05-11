# python核心学习（高级特性，字符串操作。。。）
==================================================================

## (1) 基础特性
### 1 数据类型
1. 整数  Number
2. 浮点数  Float
3. 字符串  String(可用单引号‘ ’ 以及双引号” “)
4. 布尔值  Boolean(True, False)
5. 空值  None
### 2 list/tuple/set/dict
##### list（列表）  
list是一种有序的集合，可以随时添加和删除其中的元素。  
定义一个列表
```
>>> myList = ['andy', 'viky', 'yanglin']
>>> myList
['andy', 'viky', 'yanglin']
```
获取列表长度
```
>>> len(myList)
3
```
列表插入/删除末尾元素
```
>>> myList.append('adam')
>>> myList
['andy', 'viky', 'yanglin', 'adam']

>>> myList.pop()
>>> myList
['andy', 'viky', 'yanglin']
```
列表插入/删除指定位置元素
```
>>> myList.insert(1, 'adam')
>>> myList
['andy', 'adam', 'viky', 'yanglin']

>>> myList.pop(1)
>>> myList
['andy', 'viky', 'yanglin']
```
##### tuple（元祖）
tuple和list非常类似，但是tuple一旦初始化就不能修改，但是可以修改tuple中以地址存储的元素（例如list， dict等）。
tuple的陷阱：当你定义一个tuple时，在定义的时候，tuple的元素就必须被确定下来。
```
>>> t = (1, 2)
>>> t
(1, 2)
```
定义一个元素的tuple时需要在元素后添加一个逗号
```
>>> t = (1,)
>>> t
(1,)
```
##### set（集合）
set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。
创建一个set
```
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}

// 输入重复值，结果将重复值合并
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}
```
添加/删除元素
```
>>> s.add(4)
>>> s
{1, 2, 3, 4}

>>> s.remove(4)
>>> s
{1, 2, 3}
```
##### dict（字典）
dict使用键-值（key-value）存储，具有极快的查找速度，dict是无序的。
定义一个dict
```
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
>>> d.get('Michael')
95
```
判断key是否存在于dict中
```
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> Michael in dd
True
```
根据key删除元素
```
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d.pop('Michael')
95
```
### 3 枚举
```
from enum import Enum
Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))

// 遍历所有的值
for name, member in Month.__members__.items():
    print(name, '=>', member, ',', member.value)
('Jan', '=>', <Month.Jan: 1>, ',', 1)
('Feb', '=>', <Month.Feb: 2>, ',', 2)
('Mar', '=>', <Month.Mar: 3>, ',', 3)
('Apr', '=>', <Month.Apr: 4>, ',', 4)
('May', '=>', <Month.May: 5>, ',', 5)
('Jun', '=>', <Month.Jun: 6>, ',', 6)
('Jul', '=>', <Month.Jul: 7>, ',', 7)
('Aug', '=>', <Month.Aug: 8>, ',', 8)
('Sep', '=>', <Month.Sep: 9>, ',', 9)
('Oct', '=>', <Month.Oct: 10>, ',', 10)
('Nov', '=>', <Month.Nov: 11>, ',', 11)
('Dec', '=>', <Month.Dec: 12>, ',', 12)
```
## (2) 高级特性
### 1 切片，迭代
切片在常见的编程语言中通常是使用手动循环来进行的。
在python中，对于可迭代的值：
```
// example list
myList = ['andy', 'viky', 'yanglin', 'adam']
```
获取前两个
```
>>> myList[:2]
['andy', 'viky']
```
获取第二个到第四个
```
>>> myList[1:4]
['viky', 'yanglin', 'adam']
```
取第二个到倒数第二个
```
>>> myList[1:-1]
['viky', 'yanglin']
```
取后两个
```
>>> myList[-2:]
['yanglin', 'adam']
```
空一个取出
```
>>> myList[0:4:2]
['andy', 'yanglin']
```

迭代的几种方法
常规方法
```
for (i=0; i<list.length; i++) {
    n = list[i];
}
```
for in形式
```
for key in d:
    print(key)

for key, value in d:
    print(key)
```
### 2 列表生成式

### 3 生成器，迭代器

## (3) 字符串的操作

## (4) 高阶函数
### 1 map/reduce/filter/sorted
### 2 闭包
### 3 lambda函数
### 4 装饰器，偏函数

## (5) 使用模块

## (6) IO编程
### 1 文件读写
### 2 StringIO，By tBytesIO
### 3 操作文件和目录
### 4 序列化

## (7) 进程和线程

## (8) 测试
### 1 单元测试
### 2 文档测试