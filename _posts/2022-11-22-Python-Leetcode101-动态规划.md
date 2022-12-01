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

# 一维动态规划问题

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



# 二维动态规划问题

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

# 221. [Maximal Square (Medium)](https://leetcode.cn/problems/maximal-square/)

## 题意

- `matrix`是一个`m x n`的二元矩阵
- 返回`matrix`中全是1的最大正方形面积

## 示例

![img](https://assets.leetcode.com/uploads/2020/11/26/max1grid.jpg)

```python
Input: matrix = [["1","0","1","0","0"],
                 ["1","0","1","1","1"],
                 ["1","1","1","1","1"],
                 ["1","0","0","1","0"]]
Output: 4
```

## 思路

- 定义`dp[i][j]`表示以(i, j)为右下角的全由1构成的最大正方形**边长**

- 状态转移方程`dp[i][j] = min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1]) + 1`

- 边界条件，当`matrix[i][j] = 0`时`dp[i][j] = 0 `

  ## 题解

  ```python
  class Solution(object):
      def maximalSquare(self, matrix):
          max_row, max_col = len(matrix), len(matrix[0])
          if max_row == 0 or max_col == 0:
              return 0
          length = 0
          dp = [[0 for _ in range(max_col)] for _ in range(max_row)]
          for i in range(max_row):
              for j in range(max_col):
                  if matrix[i][j] == "0":
                      dp[i][j] = 0
                  else:
                      if i == 0 or j == 0:
                          dp[i][j] = 1
                      else:
                          dp[i][j] = min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1]) + 1
                      if dp[i][j] > length:
                          length = dp[i][j]
          return length * length
  ```

  # 分割类动态规划问题

# 279. [Perfect Squares (Medium)](https://leetcode.cn/problems/perfect-squares/)

## 题意

`n`是一个在1和10000之间的正整数，求其最少可以由几个完全平方数相加构成

## 示例

```python
Input: n = 12
Output: 3
Explanation: 12 = 4 + 4 + 4.
```

## 思路

- 定义: 记`dp[i]`表示数字i最少可以由多少个平方数构成

- 状态转移方程
  $$
  dp[i]=min(dp[i-1^2]+dp[i-2^2]+dp[i-3^2]+\dots) + 1
  $$

- 边界条件: `dp[0] = 0`

## 题解

```python
class Solution(object):
    def numSquares(self, n):
        dp = [0]
        for i in range(1, n+1):
            j, temp = 1, float('inf')
            while j * j <= i:
                temp = min(temp, dp[i-j*j]+1)
                j += 1
            dp.append(temp)
        return dp[-1]
```



# 91. [Decode Ways (Medium)](https://leetcode.cn/problems/decode-ways/)

## 题意

给定一个全是数字的字符串，按照`'A' -> '1', 'Z' -> 26`的方式解码，问有多少种解码方式

## 示例

```python
Input: s = "226"
Output: 3 # "226" could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6)
```

## 思路

- 和上一个题一样，都属于“分割类”的动态规划，也就是说，`dp[i]`可能同时和多个`dp[j] (j < i)`有联系

- 定义：`dp[i]`表示到第`i`个字符为止，有多少种解码方式

- 状态转移方程
  $$
  dp[i] = valid(dp[i-1]) + valid(dp[i-2])
  $$

  - 也就是说，如果`s[i]`在`1~9`中，`dp[i]`的解码方式得加上`dp[i-1]`的
  - 如果`s[i-1:i]`在`10~26`中，`dp[i]`的解码方式得加上`dp[i-2]`的

- 边界条件：

  - `dp[0]=1`，空字符串有1种解码方式
  - 注意，这种表示方法下，`dp[1]`才表示`s[0]`的解码方式

- 压缩解空间

  - 可以用三个变量进行状态转移，不必用整个数组

# 题解

```python
class Solution(object):
    def numDecodings(self, s):
        a, b = 0, 1
        # a = dp[i-2], b = dp[i-1]
        for i in range(1, len(s)+1):
            temp = 0   # dp[i]
            if s[i-1] != '0':
                temp += b
            if i > 1 and s[i-2] != '0' and int(s[i-2:i]) <= 26:
                temp += a
            a, b = b, temp
        return temp

```





# 139. [Word Break (Medium)](https://leetcode.cn/problems/word-break/)

## 题意

- 给定一个单词表`wordDict`和一个字符串`s`

- 如果`s`可以由单词表中的词组成，返回`True`，否则返回`False`

  ## 示例

  ```python
  Input: s = "leetcode", wordDict = ["leet","code"]
  Output: true
  Explanation: Return true because "leetcode" can be segmented as "leet code".
  ```

  ## 思路

- 这一题也属于分割类型动态规划
- 和之前一样，注意用`dp[i+1]`表示`s[i]`的解情况，因为边界条件是空字符串能解

- 定义：`dp[i]`表示到第`i-1`个字符串为止(包含它本身)的解
- 状态转移：
  - 对于`wordDict`中的任意一个词`word`
  - 如果`s[i-len(word):i] == word`成立，则`dp[i] = True and dp[i-len(word)]`
- 边界条件：`dp[0] = True`

## 题解

```python
class Solution(object):
    def wordBreak(self, s, wordDict):
        dp = [True] + [False] * len(s)
        for i in range(1, len(s)+1):
            for word in wordDict:
                if i >= len(word) and s[i-len(word):i] == word:
                    dp[i] = True and dp[i-len(word)]
                    if dp[i]: break
        return dp[-1]
            
```



# 子序列问题









