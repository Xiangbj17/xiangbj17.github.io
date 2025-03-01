---
title: Intermediate Python
date: 2023-04-14 23:00:00 +0800
categories: [Intermediate, Python]
tags: [Python]
render_with_liquid: false
---

最近看了一些开源的代码，发现自己的码力尚浅。果然人还是要不断学习，不断跳出自己的舒适圈，才能有更大的进步。最近Leetcode还是要坚持刷，有时间的话（把前公司的活做完之后）尽量早点把Leetcode101解决。但是Leetcode能解决的也只是思维的部分，更多进阶的用法还是得自己找找其他的路子多看看多学。最好这几段实习完了之后，开始认真做毕设研究的时候，也搞一些开源的东西。

话休絮烦，这里给自己开一个新坑，[Intermediate Python](https://docs.pythontab.com/interpy/)，中文是《Python进阶》。主要还是自己多打打书上的案例代码，另外遇到了什么全新的领域也可以在这里做做笔记，以便日后翻阅。

# 1 *Args and **kwargs

我记得本科学C的时候似乎就看到过这个，好像还和system command有关，之前因为课时拉的太长来不及上了，这部分就沦为了未解之谜。最近在开源代码上也看到了这个，在这本书上第一个内容又碰到这个，感觉还是有一点缘分在的。

首先，这两个东西是用在**函数定义or传参**里面的。常规用法是：当我们不知道函数的user会传多少个参数进来，或者说这个函数本身就是为不定长的参数列表而设计的时候，我们就用这两个东西来接传进来的参数。

其中，

- `*args`用来接**不定长度的参数列表(like list)**
- `**kwargs`则是用来接**不定长度的键值对(like dictionary)**

其次，`*args`和`**kwargs`只是大众习惯的参数命名，这个名字是次要的，重要的其实是变量名称前面的`*`和`**`。所以以后在函数里也可以写成其他的形式，比如`*sls, **slss`或者`*var, **vars`这种，看个人喜好。

## (I) 一个星 | 函数定义

假如要写一个函数`summation()`，这个函数能接收用户传进来的所有数字，然后返回他们的加和。常规做法是把这些数字放进一个list里，然后循环加和。但如果用户比较懒，或者不懂得要将这些数字放在一个list里，就需要用到`*args`方法了。函数的写法如下：

```python
def summation(*args):
    count = 0
    for i in args:
        count += i
    return count

>>> summation(1, 2, 3, 4, 5)
15
```

那其实这么想的话，`torch.randn()`应该就是用`*args`来接受的输入，因为我不必把维度弄到一个list里面去。

## (II) 两个星星 | 函数定义

假如现在要写一个函数`pprint()`，函数需要接收用户传进来的一些参数名和参数值（有点像一个字典，其实实际接收到kwargs之后的做法也就是字典的做法），其中参数名代表人的名字，参数值为年龄，函数的功能是把这些输入按照一定的格式打印出来。那么函数的一种可能写法如下：

```python
def pprint(**kwargs):
    for key, value in kwargs.items():
        print("person:{0} -- age:{1}".format(key, value))

>>> pprint(sls=22, lyx=23, aze=24)
person:sls -- age:22
person:lyx -- age:23
person:aze -- age:24
```

## (III) 传参中的星号使用方法

在开源代码里看到过什么`*map(len, a_list)`之类的高阶用法，胡乱尝试过一通，悻悻作罢。作罢的原因都是场合没用对。首先，要使用星星，目前我看到的使用场合要么是函数定义，要么是传参。如果不涉及到这两个，就不要想星星了。下面看看传参怎么用星星。

假设现在有这么一个函数:

```python
def print_three_args(arg1, arg2, arg3):
    print("arg1:", arg1)
    print("arg2:", arg2)
    print("arg3:", arg3)
    
# 最常规的传参方式是这样的
>>> print_three_args('sls', 'lyx', 'aze')

# 那如果这三个参数已经被一个tuple或者list包起来了，我们可以这么传
>>> names = ['sls', 'lyx', 'aze']
>>> print_three_args(*names)
arg1: sls
arg2: lyx
arg3: aze

# 如果现在有一个dict，而且dict的key和函数制定的arg名称是一致的，还可以传一个**dict
>>> dict = {'arg1':'sls', 'arg2':'lys', 'arg3':'aze'}
>>> print_three_args(**dict)
arg1: sls
arg2: lys
arg3: aze
```

## (IV) 星星的使用顺序

如果想要在一个函数中同时使用一般参数、不定长列表、不定长键值对，顺序如下：

`def func(farg, *args, **kwargs):`



# 2 迭代器与生成器 | Iterators & Generators

## (I) 迭代器相关概念

- 可迭代对象**(Iterable)**：任意Python对象，只要定义了「可以返回一个迭代器的`__iter__`方法」，或者定义了「支持下标索引的`__getitem__`方法」，就是一个可迭代对象。
- 迭代器**(Iterator)**：任意Python对象，只要定义了`__next__`方法，就是一个迭代器。
- 迭代**(Iteration)**：用循环来遍历某个东西的过程，就叫做迭代。

## (II) 生成器相关概念

生成器**(Generators)**是只能进行一次迭代的迭代器。大部分生成器是以函数来实现的。他们不会返回一个值，而是暂时生出(yield)一个值。一个简单的例子如下：

```python
def my_generator():
    for i in ['sls', 'lyx', 'aze']:
        yield i

>>> for item in my_generator():
>>>     print(item, end=' ')
sls lyx aze 
```

## (III) 生成器的使用场景

在不想同一时间将所有计算出来的大量结果集分配到内存当中，特别是结果集里还包含循环时使用。

许多Python 2里的标准库函数都会返回列表，而Python 3都修改成了返回生成器，这样可以占用更少的资源。

比如，当我们想实现斐波那契数列的时候，低段位的写法是：

```python
def fibon_low(n):
    a = b = 1
    result = []
    for i in range(n):
        result.append(a)
        a, b = b, a + b
    return result

>>> fibon_low(10)
[1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```

高段位的写法是，用一个生成器，也即`yield`每一个要返回的元素，而不是返回一整个数组。这样就可以不用 担心函数是否会使用大量资源。

```python
def fibon_high(n):
    a = b = 1
    for i in range(n):
        yield a
        a, b = b, a + b

>>> for x in fibon_high(10):
>>>     print(x, end=' ')
1 1 2 3 5 8 13 21 34 55 
```

# 3 Map & Filter & Reduce

## (I) Map

用法：将一个函数映射到一个输入的list的所有元素上

规范：`map(function, list)`

使用场景：假设我们要对一个list的所有元素变成它的平方，有下面几种做法：

```python
x = [1, 2, 3, 4, 5]

# 最简单的做法
squared = [i**2 for i in x]

# 使用map的方法
squared = list(map(lambda i:i**2, x))
```

注意：一定要使用`list()`将map的返回值重新封装成一个列表，如果忘记的话，square可就是一个迭代器，而不会是一个列表了。

## (II) Filter

用法：过滤list中的元素，只有满足要求的元素才会留下来

规范：`filter(function, list)`

Example：筛选出正数`x = list(filter(lambda x: x>0, [-1, 2, -4, 3, 5, -23]))`

注意：和上面一样，也要用list还原，不然就会返回一个迭代器

## (III) Reduce

用法：对一个列表的所有元素进行某些计算，计算完之后只需要返回一个值

规范：

- `from functools import reduce`
- `reduce(function, list)`

Example：计算一个list中所有元素的乘积 `reduce(labmda x, y: x * y, [1, 2, 3, 4, 5]) => 120` 

# 4 装饰器 | Decorators

Decorator本质上是一个函数。与寻常的函数不同的是，decorator接受的input也是函数，在其内部会为这个函数添加一些功能（进行装饰）。

使用场景比如：在一个工程里，我希望所有的函数在执行的前后都打印一下函数的执行时间，如果这个功能在每个函数里都直接写，就太繁琐了。这时候，就可以写一个打印时间的装饰器，然后给每个函数都给装上。

```python
import time
from functools import wraps 

def print_time(some_func):
    # 这里如果不用wraps，那么some_func.__name__就会返回wrapMyFunction，显然是不希望的
    @wraps(some_func)
    def wrapMyFunction():
        print('*** Func:', some_func.__name__)
        start = time.time()
        some_func()
        Time = time.time() - start
        print('*** time used:', Time)
    return wrapMyFunction
        
    
@print_time
def cal_100():
    count = 0
    for i in range(100):
        count += i
    print('sum of 1 to 100 is:', count)
    
@print_time
def print_hello():
    print('Hello!')
    
# 验证一下是否可行
>>> cal_100()
*** Func: cal_100
sum of 1 to 100 is: 4950
*** time used: 4.124641418457031e-05

# 第二个函数的验证
>>> print_hello()
*** Func: print_hello
Hello!
*** time used: 7.700920104980469e-05
```



# 5 处理异常

简单的处理异常方式:`try & except`

```python
try:
  file = open('aaa.txt', 'r')
except IOError as e:
  print('An IOError occur. {}'.format(e.args[-1]))
```

## (I) 处理多个异常

有三种方式，记一种就行了：把所有异常放到一个tuple里：

```python
try:
	file = open('aaa.txt'. 'r')
except (IOError, EOFError) as e:
  print("Error occurred. {}".format(e.args[-1]))
```

## (II) finally从句

包裹到finally从句中的代码不管异常是否触发都将会被执行。

这可以被用来在脚本执行之后做清理工作。一个简单的例子如下：

```python
try:
    file = open('test.txt', 'rb')
except IOError as e:
    print('An IOError occurred. {}'.format(e.args[-1]))
finally:
    print("This would be printed whether or not an exception occurs.")
```





# 6 函数缓存 | Function caching

当一个I/O密集的函数被频繁使用相同的参数调用的时候，函数缓存可以节约时间。在Python 3.2以后版本，有个lru_cache的装饰器，允许我们将一个函数的返回值快速地缓存或取消缓存。

下面是一个使用lru_cache的斐波那契计算器样例：

```python
from functools import lru_cache

@lru_cache(maxsize=32) # 最多缓存最近多少个返回值
def fib(n):
  if n < 2:
    return n
  else:
    return fib(n-1) + fib(n)
 
>>> print([fib(n) for n in range(10)])
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

>>> fib.cache_clear()
# 清除缓存
```





# 7 上下文管理器 | Context Managers

上下文管理器可以在有需要的时候，精确地分配和释放资源。最广泛的案例是with语句，一个例子如下：

```python
with open('aa.txt', 'w') as file:
  file.write('Hello!')
  
# 上面的代码和下面这一段代码等价

file = open('aa.txt', 'w')
try:
  file.write('Hello')
finally:
  file.close()

```

with语句的主要优势在于确保我们的文件会被关闭，而不用关注嵌套代码如何退出。





