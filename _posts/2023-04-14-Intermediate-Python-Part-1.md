---
title: Intermediate Python | Part 1
date: 2023-04-14 23:00:00 +0800
categories: [Intermediate, Python]
tags: [Python]
render_with_liquid: false
---

最近看了一些开源的代码，发现自己的码力尚浅，类井底之蛙。果然人还是要不断学习，不断跳出自己的舒适圈，才能有更大的进步。最近Leetcode还是要坚持刷，有时间的话（把前公司的活做完之后）尽量早点把Leetcode101解决。但是Leetcode能解决的也只是思维的部分，更多进阶的用法还是得自己找找其他的路子多看看多学。最好这几段实习完了之后，开始认真做毕设研究的时候，也搞一些开源的东西。

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