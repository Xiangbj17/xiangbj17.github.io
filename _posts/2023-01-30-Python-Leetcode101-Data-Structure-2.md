---
title: Python Leetcode101 利用数据结构(2):哈希表
date: 2023-02-02 16:52:00 +0800
categories: [Leetcode, Data Structure]
tags: [Algorithm]
render_with_liquid: false
---

# 1 Hash Table & Dict | 哈希表和多功能字典

## (I) What | 哈希表概念

哈希表是一种通过key来访问value存储位置的数据结构，而「将key映射到存储位置」的函数称为哈希函数。

## (II) Hash Table in Python

Python中的字典dict就是用哈希表实现的，知道这个就可以。

关于如何自己创建一个哈希表的Class，以及如何解决冲突问题，更详细的内容可以在这个[链接](https://python123.io/index/topics/data_structure/hash_table)里看到。

下面给出一些刷题的时候会起到帮助作用的非原生dict，主要有：

| Dict的子类  |             功能              |                     个人感觉                     |
| :---------: | :---------------------------: | :----------------------------------------------: |
| OrderedDict |    可以保留key被加入的顺序    |         可能会用`popitem()`去干一些事情          |
|   Counter   |            计数器             | 实用，特别是`most_common()`，以及list`(Counter)` |
| defaultdict | 若key不存在则自动为它创建条目 |  也很实用，免得每次还要`if key in dict.keys()`   |

## (III) OrderedDict()

基本使用以及性质

```python
>>> from collections import OrderedDict
>>> 
>>> od = OrderedDict()  # 创建一个OrderedDict()
>>> od['b'] = 2
>>> od['c'] = 3
>>> od['a'] = 1
>>> od
OrderedDict([('b', 2), ('c', 3), ('a', 1)])

# 现在去更改od里面某一个元素的值，key的排序还是不变
>>> od['b'] = 2.0
>>> od
OrderedDict([('b', 2.0), ('c', 3), ('a', 1)])

```

可以使用的功能

```python
# popitem(last=True)，返回尾部key和value，传入False则返回头部
>>> od.popitem()
('a', 1)

# move_to_end(key, last=True)，把一个现有的key移动到字典的任一端
>>> od.move_to_end('b', last=True)
>>> od
OrderedDict([('c', 3), ('a', 1), ('b', 2.0)])
```



## (IV) Counter

一个计数器，基本使用方式如下

```python
>>> from collections import Counter

# 通过迭代对象生成一个Counter
>>> c = Counter('Hannibal')
>>> c
Counter({'a': 2, 'n': 2, 'H': 1, 'i': 1, 'b': 1, 'l': 1})
>>> 

# 通过一个映射(dict)生成Counte
>>> c1 = Counter({'apple':2, 'pear':5})
>>> c1
Counter({'pear': 5, 'apple': 2})

# 通过关键字参数生成
>>> c2 = Counter(s=1, n=2, h=48)
>>> c2
Counter({'h': 48, 'n': 2, 's': 1})

# 因为Counter实现了字典的__missing__方法，所以如果访问一个不存在的key，返回0
>>> c['c']
0
```

常用方法

```python
# most_common(n) 返回n个最常见的元素及出现的次数，由高到低排序
>>> c.most_common(3)
[('a', 2), ('n', 2), ('H', 1)]

# elements()，返回一个迭代器，元素按照首次出现的顺序返回
>>> for i in c.elements():
...     print(i, end=',')
... 
		H,a,a,n,n,i,b,l,

# list(Counter)， 不重复地列出所有元素
>>> list(c)
['H', 'a', 'n', 'i', 'b', 'l']

# update()，给某个key的value再加一个值，而不是替换
>>> c['a'] = 3
>>> c
Counter({'a': 3, 'n': 2, 'H': 1, 'i': 1, 'b': 1, 'l': 1})
>>> c.update({'a':4})
>>> c
Counter({'a': 7, 'n': 2, 'H': 1, 'i': 1, 'b': 1, 'l': 1})
```

## (V) defaultdict

常规使用方法

```python
>>> from collections import defaultdict

# 创建一个默认int类型的defaultdict
>>> dfd = defaultdict(int)

# 询问一个不存在的key，默认给出 0
>>> dfd['s']
0
>>> dfd
defaultdict(<class 'int'>, {'s': 0})
```



# 1. [Two Sum (Easy)](https://leetcode.cn/problems/two-sum/)

## 题目

给定一个整数数组，已知有且只有两个数的和等于给定值，求这两个数的位置。

## Example

```py
nums = [2,7,11,15], target = 9
res = [0,1]
```

## 思路

- Note that 可以暴力解，双循环的话时间复杂度是`O(n^2)`，没有超时

  - ```python
    	class Solution(object):
        """
        执行用时：1292 ms, 在所有 Python 提交中击败了49.98%的用户		 
    		内存消耗：13.9 MB, 在所有 Python 提交中击败了37.26%的用户
    		"""
        def twoSum(self, nums, target):
            for i in range(len(nums)):
                t2 = target - nums[i]
                for j in range(i+1, len(nums)):
                    if nums[j] == t2:
                        return [i, j]
    ```

- Hash表解法，时间复杂度`O(n)`，空间复杂度也是`O(n)`

  - 使用一个dict，将已经搜索过的索引`i`对应所需的另一个数`target-nums[i]`和`i`的索引存储起来

  - `for i in range(len(nums))`，如果`nums[i]`已经在dict当中了，返回当前索引，及`dict`对应`value`

    

## 题解

```python
class Solution(object):
    def twoSum(self, nums, target):
        """
				执行用时：368 ms, 在所有 Python 提交中击败了58.97%的用户
				内存消耗：13.8 MB, 在所有 Python 提交中击败了42.65%的用户
        """
        d = {}
        for i in range(len(nums)):
            if not d or nums[i] not in d.keys():
                d[target - nums[i]] = i
            else:
                return [i, d[nums[i]]]
```

