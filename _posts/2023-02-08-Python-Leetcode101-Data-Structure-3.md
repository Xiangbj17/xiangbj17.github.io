---
title: Python Leetcode101 利用数据结构(3):多重集合、映射、前缀、积分图
date: 2023-02-08 21:31:00 +0800
categories: [Leetcode, Data Structure]
tags: [Algorithm]
render_with_liquid: false
---

# 多重集合和映射

# 332. [Reconstruct Iteneray (Hard)](https://leetcode.cn/problems/reconstruct-itinerary/)

## 题目

给定一个人坐过的一些飞机的起止机场，已知这个人从 `JFK` 起飞，问这个人是按什么**顺序**飞的；

如果存在多种可能性，返回字母序最小的那种。

## Example

```python
tickets = [["MUC","LHR"],["JFK","MUC"],["SFO","SJC"],["LHR","SFO"]]
res = ["JFK","MUC","LHR","SFO","SJC"]

```

## 思路

简化题意：给定一个`n`个点，`m`条边的图，求从给定的点出发，恰巧经过所有的边一次的路径。

题目要求字典序最小，那么我们在此过程中按照字典序最小优先选择，进行深度优先搜索，只要找到了叶节点即为解。

## 题解

```python
class Solution(object):
    def findItinerary(self, tickets):
        def dfs(port):
            while d[port]:
                dest = heapq.heappop(d[port])
                dfs(dest)
            res.append(port)
        # 首先，用一个hash来存储从每个顶点开始的边
        d = defaultdict(list)
        for depart, arrive in tickets:
            d[depart].append(arrive)
        # 使用heapq对其进行排序[满足题目字典序的要求]
        for key in d:
            heapq.heapify(d[key])

        res = []
        # 进行深搜
        dfs("JFK")
        return res[::-1]
```

# 前缀和与积分图

一维的**前缀和**，二维的**积分图**，都是把每个位置之前的一维线段或二维矩形**预先存储**，方便加速计算。

- 如果需要对前缀和或积分图的值做寻址，则要存在哈希表里；
- 如果要对每个位置记录前缀和或积分图的值，则可以储存到一维或二维数组里，也常常伴随着动态规划。

# 303. [Range Sum Query-Immutable](https://leetcode.cn/problems/range-sum-query-immutable/submissions/)

## 题目

实现一个`NumArray`类，使得其能够快速查询给定数组中，任意两个位置间所有数字的和。

## Example

```python
# 输入：
["NumArray", "sumRange", "sumRange", "sumRange"]
[[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]
# 输出：
[null, 1, -1, -3]

# 解释：
NumArray numArray = new NumArray([-2, 0, 3, -5, 2, -1]);
numArray.sumRange(0, 2); // return 1 ((-2) + 0 + 3)
numArray.sumRange(2, 5); // return -1 (3 + (-5) + 2 + (-1)) 
numArray.sumRange(0, 5); // return -3 ((-2) + 0 + 3 + (-5) + 2 + (-1))

```

## 思路

使用前缀和来解决此类问题。用一个数组存储`nums`每个位置前所有数的和，然后直接用索引一减就能快速查值

## 题解

```python
class NumArray(object):
    def __init__(self, nums):
        self.res = [0]
        for i in range(len((nums))):
            self.res.append(self.res[i]+ nums[i])
    def sumRange(self, left, right):
        return self.res[right+1] - self.res[left]
```

