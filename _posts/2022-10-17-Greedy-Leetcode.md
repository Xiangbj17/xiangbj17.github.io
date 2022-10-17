---
title: Python Leetcode101 贪心算法
date: 2022-10-17 21:08:00 +0800
categories: [Leetcode, Greedy]
tags: [Algorithm]
render_with_liquid: false

---

# 贪心原则

应该是通过「每次都达到局部最优」的策略实现一个**全局最优**

难点在于设计达到全局最优的局部最优贪心策略

# 455. [Assign Cookies (Easy)](https://leetcode.cn/problems/assign-cookies/)

## 题目大意

- `g[i]:` 表示第`i`个孩子满足胃口的饼干最小尺寸
- `s[j]:` 表示我现在手里的饼干有哪些
- 要求：问我现在手里的饼干最多能满足多少个孩子，输出这个数

## 示例

```python
g = [1, 2, 3], s = [1, 1]
res = 1
```

## 思路

**重要的有三点**：

1. 题目的要求是【最多的孩子】，所以从孩子的角度去考虑问题。饥饿度越大的孩子越不容易满足，所以贪心策略是：<u>把饼干分给那些不太饿的</u>，以求人数最多。
2. 我们需要找“当前饥饿度最小的孩子”以及当前最小的饼干数量，这涉及到<u>排序</u>。所以，g和s数组都应该被排序。
3. 用**指针**， 终止条件为「任意一个指针走到对应list的末尾」，所以这个循环用while。如果匹配上，孩子和饼干指针都往前走；如果当前饼干不能满足孩子，饼干指针往前走。一个比较取巧的地方在于<u>循环终止的时候，孩子指针的位置刚好就是所求</u>。

我第一次做的时候：

- 能想到1
- 用了2但不是很敢确定，犹豫了一段时间才写上sort
- 3完全没想到，用了for循环，而且用res记录这个结果，浪费了内存

## 题解

```python
class Solution(object):
    def findContentChildren(self, g, s):
        g.sort()
        s.sort()
        child, cookie = 0, 0
        while child < len(g) and cookie < len(s):
            if g[child] <= s[cookie]:
                child += 1
            cookie += 1
        return child
```



# 135. [Candy (Hard)](https://leetcode.cn/problems/candy/)

## 题目大意

- `ratings: `代表每个孩子的评分
- 给每个孩子分发糖果，规则如下，返回需要准备的最少糖果数目
  - 每个孩子至少分到一个糖果
  - 相邻的孩子中，评分更高的需要获得更多的糖果

## 示例

```python
ratings = [1,0,2]
res = 5  # [2, 1, 2]
```

```python
ratings = [1,2,2]
res = 4  # [1,2,1]
```

## 思路

​	我大概永远也不会想到，贪心是在于“每次遍历中，只考虑并更新相邻一侧的大小关系。”也就是说：

- 先给所有的孩子1颗糖
- 从左往右遍历一遍，如果右边的孩子评分更高，那右边的糖果+1；
- 再从右往左遍历一遍，如果左边的孩子评分更高：
  - 如果左边孩子的糖果数已经大于右边孩子了，保持
  - 如果左边孩子的糖果数小于等于右边孩子，+1

## 题解

```python
class Solution(object):
    def candy(self, ratings):
        if len(ratings) == 1:
            return 1        
        res = [1] * len(ratings)
        for i in range(len(ratings)-1):
            if ratings[i+1] > ratings[i]:
                res[i+1] = res[i] + 1       
        for i in range(len(ratings)-1, 0, -1):
            if ratings[i-1] > ratings[i]:
                res[i-1] = max(res[i-1], res[i]+1)   
        return sum(res)
```

