---
title: Python Leetcode101 动态规划
date: 2022-11-22 22:56:00 +0800
categories: [Leetcode, DynamicProgramming]
tags: [Algorithm]
render_with_liquid: false

---

无语子，阿根廷爆冷，下半场全员心态破防根本拿不住球，气死了。

# 动态规划(Dynamic Programming, DP)

- 一些概念和用途

  - 动态规划用于查找有很多**重叠子问题**的情况的最优解
  - 它将问题重新组合成子问题，从简单的问题出发，直到整个问题都被解决
  - 并且为了避免多次重复计算一些子问题，所有结果都被保存起来

- 一些关键点

  - 找到状态转移方程，感觉就跟做数列题一样，找f(n)和f(n-1), f(n-2)，...的关系。
    $$
    f(n) = g(f(n-1), f(n-2),..., f(1))
    $$

  - 找边界条件，也就是最底层的f(0)等于几，f(1)等于几

  - 计算和储存子问题的解



# 70. [Climbing Stairs （Easy）](https://leetcode.cn/problems/climbing-stairs/)

## 题意

- 假设你一次可以爬1级或者2级台阶，现在有`n`级台阶

- 求：有多少种可以爬到楼顶的方法

  ## 示例

  ```python
  n = 3
  res = 3 # [1,2], [2, 1], [1, 1, 1]
  ```

  ## 思路

  重点就是找到状态转移方程嘛，这道题很简单，就是：
  $$
  f(n)=f(n-1)+f(n-2)
  $$
  然后最底层的话
  $$
  f(1) = 1,\; f(2) = 2
  $$

  ## 题解

  ```python
  class Solution(object):
      def climbStairs(self, n):
          s=[1,2]
          if n<=2:
              return s[n-1]
          while len(s)<n:
              s.append(s[-1]+s[-2])
          return s[-1]
  ```

  