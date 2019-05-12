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
<strong>list（列表）</strong>
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
<strong>tuple（元祖）</strong>
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
<strong>set（集合）</strong>
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
<strong>dict（字典）</strong>
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
列表生成式即List Comprehensions，是Python内置的非常简单却强大的可以用来创建list的生成式。
使用list生成
```
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```
利用循环
```
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```
使用if条件语句
```
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```
双重循环
```
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```
### 3 生成器，迭代器
<strong>生成器：</strong>
受到内存限制，列表容量肯定是有限的，如果我们仅仅需要访问前面几个元素，我们可以使用generator。在Python中，这种一边循环一边计算的机制，称为生成器：generator。
创建一个generator
```
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>
```
一个一个打印出来，通过next()函数获得generator的下一个返回值。
```
>>> next(g)
0
>>> next(g)
1
```
使用循环打印
```
>>> g = (x * x for x in range(10))
>>> for n in g:
...     print(n)
```
使用generator函数（在普通函数中加上yield关键字）。
```
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```
在使用for循环时获取不到return出来的值，如果需要return的值需要捕获StopIteration错误。
```
>>> g = fib(6)
>>> while True:
...     try:
...         x = next(g)
...         print('g:', x)
...     except StopIteration as e:
...         print('Generator return value:', e.value)
...         break
```
<strong>迭代器：</strong>
可以直接作用于for循环的数据对象成为可迭代对象：Iterable。
可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator。
生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator。
## (3) 字符串的操作
详细描述：
_https://www.runoob.com/python/python-strings.html_
## (4) 高阶函数
### 1 map/reduce/filter/sorted
<strong>map:</strong>
map的第一个参数为function，第二个参数为Iterable对象。map的返回值为一个惰性序列Iterator。
```
>>> def f(x):
...     return x * x
...
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> list(r)
[1, 4, 9, 16, 25, 36, 49, 64, 81]
```
<strong>reduce：</strong>
reduce把一个函数作用在一个序列[x1, x2, x3, ...]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算，其效果就是：
```
>>> from functools import reduce
>>> def add(x, y):
...     return x + y
...
>>> reduce(add, [1, 3, 5, 7, 9])
25
```
<strong>filter：</strong>
filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。
```
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
```
<strong>sorted：</strong>
```
>>> sorted([36, 5, -12, 9, -21])
[-21, -12, 5, 9, 36]

>>> sorted([36, 5, -12, 9, -21], key=abs)
[5, 9, -12, -21, 36]
```
反向排序
```
>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
['Zoo', 'Credit', 'bob', 'about']
```
### 2 闭包
在函数中返回函数，内部自成一个闭包，保存内部变量的值。
ps：返回函数不要引用任何循环变量，或者后续会发生变化的变量。
```
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum
```
### 3 lambda函数
当我们在传入函数时，有些时候，不需要显式地定义函数，直接传入匿名函数更方便。
```
>>> list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
[1, 4, 9, 16, 25, 36, 49, 64, 81]

// 其中的lambda函数相当于
def f(x):
    return x * x
```
### 4 装饰器，偏函数
<strong>装饰器：</strong>
decorator就是一个返回函数的高阶函数。
定义一个日志装饰器。
wrapper()函数的参数定义是(*args, **kw)，因此，wrapper()函数可以接受任意参数的调用。在wrapper()函数内，首先打印日志，再紧接着调用原始函数。
```
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```
运用装饰器。
```
@log
def now():
    print('2015-3-25')
```
如果需要在装饰器中传入参数。
```
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator

@log('execute')
def now():
    print('2015-3-25')
```
<strong>偏函数：</strong>
偏函数的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。
```
>>> import functools
>>> int2 = functools.partial(int, base=2)
>>> int2('1000000')
64
```
## (5) 使用模块
以下为标准模块模版，使用模块可以很好的将功能区分，使得项目结构更加清晰。
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

' a test module '

__author__ = 'Andy'

import sys

def test():
    args = sys.argv
    if len(args)==1:
        print('Hello, world!')
    elif len(args)==2:
        print('Hello, %s!' % args[1])
    else:
        print('Too many arguments!')

if __name__=='__main__':
    test()
```
## (6) IO编程
### 1 文件读写
以下为读文件的一个流程。
```
>>> f = open('/Users/michael/test.txt', 'r')
>>> f.read()
'Hello, world!'
>>> f.close()
```
一般读取文件时都需要捕获异常。
```
try:
    f = open('/path/to/file', 'r')
    print(f.read())
finally:
    if f:
        f.close()
  
或者
with open('/path/to/file', 'r') as f:
    print(f.read())
```
如果文件较大，建议多次读取。
```
with open('/path/to/file', 'r') as f:
    print(f.read(size))
```
读取二进制文件，比如图片、视频等等。
```
>>> f = open('/Users/michael/test.jpg', 'rb')
>>> f.read()
b'\xff\xd8\xff\xe1\x00\x18Exif\x00\x00...' # 十六进制表示的字节
```
要读取非UTF-8编码的文本文件。
```
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk')
>>> f.read()
'测试'
```
写入文件只需要将上述f.read()方法改为f.write()方法即可，其余一致。
### 2 StringIO，BytesIO
StringIO顾名思义就是在内存中读写str。
```
>>> from io import StringIO
>>> f = StringIO()
>>> f.write('hello')
5
>>> f.write(' ')
1
>>> f.write('world!')
6
>>> print(f.getvalue())
hello world!
```
如果要操作二进制数据，就需要使用BytesIO。
```
>>> from io import BytesIO
>>> f = BytesIO()
>>> f.write('中文'.encode('utf-8'))
6
>>> print(f.getvalue())
b'\xe4\xb8\xad\xe6\x96\x87'
```
### 3 操作文件和目录
```
# 查看当前目录的绝对路径:
>>> os.path.abspath('.')
'/Users/michael'
# 在某个目录下创建一个新目录，首先把新目录的完整路径表示出来:
>>> os.path.join('/Users/michael', 'testdir')
'/Users/michael/testdir'
# 然后创建一个目录:
>>> os.mkdir('/Users/michael/testdir')
# 删掉一个目录:
>>> os.rmdir('/Users/michael/testdir')
# 拆分路径：
>>> os.path.split('/Users/michael/testdir/file.txt')
('/Users/michael/testdir', 'file.txt')
# 直接获得文件扩展名：
>>> os.path.splitext('/path/to/file.txt')
('/path/to/file', '.txt')
# 对文件重命名:
>>> os.rename('test.txt', 'test.py')
# 删掉文件:
>>> os.remove('test.py')
```
### 4 序列化/JSON
<strong>序列化</strong>
序列化在Python中叫pickling，反序列化为unpickling。
将一个对象序列化。
```
>>> import pickle
>>> d = dict(name='Bob', age=20, score=88)
>>> pickle.dumps(d)
b'\x80\x03}q\x00(X\x03\x00\x00\x00ageq\x01K\x14X\x05\x00\x00\x00scoreq\x02KXX\x04\x00\x00\x00nameq\x03X\x03\x00\x00\x00Bobq\x04u.'
```
将对象序列化并直接写入文件。
```
>>> f = open('dump.txt', 'wb')
>>> pickle.dump(d, f)
>>> f.close()
```
加载bytes使用pickle.loads()。
从文件直接加载。
```
>>> f = open('dump.txt', 'rb')
>>> d = pickle.load(f)
>>> f.close()
>>> d
{'age': 20, 'score': 88, 'name': 'Bob'}
```
<strong>JSON</strong>
将一个对象变为json字符串。
```
>>> import json
>>> d = dict(name='Bob', age=20, score=88)
>>> json.dumps(d)
'{"age": 20, "score": 88, "name": "Bob"}'
```
加载json生成对象使用json.loads(d)。
如果对象是一个class，那么json.dumps()无法直接将class变为json字符串。  
我们可以使用dumps中的另一个参数，先将class转为dict。  
json.dumps(s, default=lambda obj: obj.__dict__))  
加载时需要先读取dict，再利用钩子函数将dict转为class。  
json.loads(json_str, object_hook=dict2class)

##### 部分内容来自于_https://www.liaoxuefeng.com/wiki/1016959663602400_
##### 该博文仅作学习使用。