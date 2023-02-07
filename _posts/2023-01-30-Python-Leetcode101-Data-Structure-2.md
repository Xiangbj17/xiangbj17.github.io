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



# 128. [Longest Consecutive Sequence (Medium)](https://leetcode.cn/problems/longest-consecutive-sequence/)

## 题意

给定一个乱序的数组`nums`，找出其中最长的连续序列，不要求数在数组中挨在一起。

要求设计一个时间复杂度为`O(n)`的算法

## Example

```python
nums = [100,4,200,1,3,2]
res = 4
# 最长数字连续序列是 [1, 2, 3, 4], 它的长度为 4。
```

## 思路

### 为什么哈希表耗时低

要求时间复杂度为`O(n)`了，那么就肯定与sort无瓜，也不要去考虑什么左右指针什么的了，只能用内存换时间。

hash table确实是一个好东西，比如：

- 我要查`4`这个元素在不在一个`list`里面，我需要用`4 in list`来查询，这个复杂度是`O(n)`级别的
- 但如果我要查它在不在`hash table`里，我只需要看`hash[4]`有没有值即可，也`dict.get()`方法，复杂度`O(1)`

这下问题就简单了，我们把题目给出的`nums`组织成`hash table`，然后查询，就可以使得时间复杂度大大减小

### 如何利用表来求最长连续序列

假设我们为上面的example建立了一个表：`{100:True, 4:True, 200:True, ...}`【遍历一次】

每次我们从中`popitem()`：【遍历第二次】

- 比如拿到一个`100`， 我们去查表里有没有99和101，没有的话就算了。
- 第二次，我们拿到4，我们去查表里有没有3，发现有3，进而再发现2和1，边查边删，并记录下来这个长度。

这样，我们也只遍历了两次，两次的操作都是`O(n)`级别的，所以最后也是`O(n)`

## 题解

```python
class Solution(object):
    def longestConsecutive(self, nums):
        if not nums:
            return 0
        d = {}
        for num in nums:
            d[num] = True
        res = 1
        while d:
            temp_num, _ = d.popitem()
            prev, nex = temp_num - 1, temp_num + 1
            while d.get(prev, False):
                del d[prev]
                prev -= 1
            while d.get(nex, False):
                del d[nex]
                nex += 1 
            temp_len = nex - prev - 1
            res = max(res, temp_len)
        return res
```

# 149. [Max Points On A Line (Hard)](https://leetcode.cn/problems/max-points-on-a-line/)

## 题目

给定一个数组`points`，每个元素表示坐标系中的一个点，求有多少个点在同一条直线上

## Example

<img src="https://assets.leetcode.com/uploads/2021/02/25/plane2.jpg" alt="img" style="zoom:50%;" />

```python
points = [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]
res = 4
```

## 思路

使用「一个点 + 一条斜率」确定一条直线法。

那么，以每个点`(x_k, y_k)`为基准点，计算其他的点与它的斜率，用`hash`表存储每个斜率对应有多少个点即可。

可以取巧的地方：以第`i`个点为基准的时候，只需要看第`i+1`个点后面的情况，因为前面已经考虑过`i`前面的点了

## 题解

```python
class Solution(object):
    def maxPoints(self, points):
      """用时48ms，击败98.21%；内存13.1MB，击败58.93%"""
        if len(points) <= 2:
            return len(points)
        res = 0
        d = defaultdict(int)
        for i in range(0, len(points)):
            horizon, vertical = 0, 0
            temp_max = 0
            for j in range(i+1, len(points)):
                if points[j][0] == points[i][0]:
                    vertical += 1
                elif points[i][1] == points[j][1]:
                    horizon += 1
                else:
                    slope = float(points[j][1] - points[i][1]) / float(points[j][0] - points[i][0])
                    d[slope] += 1
                    temp_max = max(temp_max, d[slope])
            temp_max = max(horizon, vertical, temp_max)
            res = max(res, temp_max)
            d.clear()
        return res+1
```

