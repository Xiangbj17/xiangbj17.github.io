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

  - 找到**状态转移方程**，感觉就跟做数列题一样，找f(n)和f(n-1), f(n-2)，...的关系。
    $$
    f(n) = g(f(n-1), f(n-2),..., f(1))
    $$

    - 这个**f(n)的定义**也是很重要的一点
    
    - 比如：
      - f(n)代表到第n个数为止，新形成的xx的数量
    
      - 或者f(n)代表，到第n个数位置，总共形成的xx的数量
    
    - 上面这个定义是很重要的，可以解决整体问题，也可以解决分问题，**怎么找规律容易怎么来反正**
    
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

  # 413. [Arithmetic Slices (Medium)](https://leetcode.cn/problems/arithmetic-slices/)

## 题意

- 长度大于等于三的，间隔相等的数组称为等差数列

- 给一个数组`nums`，求其中包含的等差数列的个数

  ## 示例

  ```python
  nums = [1, 2, 3, 4]
  res = 3 # [1, 2, 3], [2, 3, 4], [1, 2, 3, 4]
  ```

## 思路

这题主要在于如何去定义`f(n)`，也就是代码里的`dp[i]`

从下面的图很容易发现这样一个规律（图摘自Leetcode）

<img src="https://pic.leetcode-cn.com/1628582064-PctBWQ-image.png" alt="image.png" style="zoom:67%;" />

如果定义`dp[i]`为到第`i`个数为止，*新产生* 的等差数列个数，那这个规律就很好找
$$
dp[i] = dp[i-1]+1\;\;\{if \;\;nums[i]-nums[i-1]=nums[i-1]-nums[i-2]\}
\\
dp[i]=0 \;\;\{if \;\;nums[i]-nums[i-1] \neq nums[i-1]-nums[i-2]\}
$$
那么，需要求的`res`其实就是`dp[0]+...+dp[i]`的总和

代码就写出来了

但如果定义`f[n]`就是要求的`res`，其实规律还不是那么好找。

## 题解

```python
class Solution(object):
    def numberOfArithmeticSlices(self, nums):
        if len(nums) < 3:
            return 0
        res = 0
        dp = 0
        for i in range(2, len(nums)):
            if nums[i]-nums[i-1] == nums[i-1]-nums[i-2]:
                dp += 1
                res += dp
            else:
                dp = 0 # 注意如果某个地方等差数列断开了，dp需要清零，否则会多算的
        return res
```



# 542. [ 01 Matrix (Medium)](https://leetcode.cn/problems/01-matrix/)

## 题意

- 有一个`m x n`的二元矩阵`mat`，里面至少有一个0
- 为每个格子返回离其最近的0的距离

## 示例

![img](https://assets.leetcode.com/uploads/2021/04/24/01-2-grid.jpg)

```python
Input: mat = [[0,0,0],[0,1,0],[1,1,1]]
Output: [[0,0,0],[0,1,0],[1,2,1]]
```

## 思路

- 这个题可以抽象成多个起始节点的广度优先搜索（也称多源BFS）。具体解法是，先找到所有的0，然后一层一层地搜，一直到queue里面没有节点为止。
- 动态规划也可，先左上角到右下角遍历一次，再从右下角到左上角搜索一次更新结果，这样复杂度就还是O(mn)

其实左上→右下+右下→左上两次搜索的方式在之前有道Hard的题也见过，得把这个技巧记一下。

记`dp[i][j]`表示离节点最近的0的距离，那么动态规划的状态转移方程：

- 在从左上→右下的case中为：

$$
\begin{align*}
	\begin{split}
		dp[i][j]= 
	\left \{
	\begin{array}{ll} 
			min(dp[i-1][j], dp[i][j-1])+1),   & mat[i][j]\neq0\\
    	0,                                 & mat[i][j]=0
	\end{array}
	\right.
\end{split}
 \end{align*}
$$

边界条件是`mat[i][j]=0`的情况，且这一次搜索要把全部的零都给找到

- 在从左上→右下的case中为：

$$
dp[i][j]= min(dp[i][j], \;dp[i][j+1])+1,\;dp[i+1][j])+1)
$$

且只搜索`mat[i][j]=1`的情况，因为只需要更新这些节点的值

## 代码

```python
"""DP的代码，用时136 ms，内存消耗16.5MB"""
class Solution(object):
    def updateMatrix(self, mat):
        m, n = len(mat), len(mat[0])
        dp = [[float('inf') for _ in range(n)] for _ in range(m)]
        for i in range(m):
            for j in range(n):
                # 这一轮搜索里，如果mat[0][0] == 1 的话，其实是不会搜到的
                if mat[i][j] == 0:
                    dp[i][j] = 0
                else:
                    if j > 0:
                        # 避免搜到[*][-1]溢出
                        dp[i][j] = min(dp[i][j], dp[i][j-1] + 1)
                    if i > 0:
                        dp[i][j] = min(dp[i][j], dp[i-1][j] + 1)
        for i in range(m-1, -1, -1):
            for j in range(n-1, -1, -1):
                if mat[i][j] != 0:
                    # 这一轮只对那些1的值进行更新
                    if i < m - 1:
                        # 避免搜到[m][*]溢出
                        dp[i][j] = min(dp[i][j], dp[i+1][j]+1)
                    if j < n - 1:
                        dp[i][j] = min(dp[i][j], dp[i][j+1]+1)
        return dp
```

```python
"""多源BFS的代码，用时304 ms，内存消耗16.3 MB"""
class Solution(object):
    def updateMatrix(self, mat):
        m, n = len(mat), len(mat[0])
        direction = [-1, 0, 1, 0, -1]
        Q = collections.deque([])
        visited = set()
        # 初始化队列，加入所有的起点
        for i in range(m):
            for j in range(n):
                if mat[i][j] == 0:
                    Q.append((i, j))
                    visited.add((i, j))
        while Q:
            i, j = Q.popleft()
            for di in range(4):
                x, y = i + direction[di], j + direction[di+1]
                if 0 <= x < m and 0 <= y < n and (x, y) not in visited:
                    mat[x][y] = mat[i][j] + 1
                    visited.add((x, y))
                    Q.append((x, y))
        return mat
```

