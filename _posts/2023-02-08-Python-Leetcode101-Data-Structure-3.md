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

# 303. [Range Sum Query-Immutable (Easy)](https://leetcode.cn/problems/range-sum-query-immutable/submissions/)

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



# 304. [Range Sum Query 2d Immutable (Medium)](https://leetcode.cn/problems/range-sum-query-2d-immutable/)

## 题目

实现一个`NumMatrix`类，快速计算矩形的子矩形范围内的元素总和

## Example

<img src="https://pic.leetcode-cn.com/1626332422-wUpUHT-image.png" alt="img" style="zoom:50%;" />

```python
["NumMatrix","sumRegion","sumRegion","sumRegion"]
[[[[3,0,1,4,2],[5,6,3,2,1],[1,2,0,1,5],[4,1,0,1,7],[1,0,3,0,5]]],[2,1,4,3],[1,1,2,2],[1,2,2,4]]
# 输出: 
[null, 8, 11, 12]

#解释:
#NumMatrix numMatrix = new NumMatrix([[3,0,1,4,2],[5,6,3,2,1],[1,2,0,1,5],[4,1,0,1,7],[1,0,3,0,5]]);
#numMatrix.sumRegion(2, 1, 4, 3); // return 8 (红色矩形框的元素总和)
#numMatrix.sumRegion(1, 1, 2, 2); // return 11 (绿色矩形框的元素总和)
#numMatrix.sumRegion(1, 2, 2, 4); // return 12 (蓝色矩形框的元素总和)
```

## 思路

和上一题一样，但在做的过程中有三个地方要注意：

1. 新建一个`self.res`存储`accumulate[i][j]`的时候，为了计算方便，`res`的大小是`m+1, n+1`，第一行和第一列都补零

   - 而且这样做也是必要的
   - 因为如果要计算`[i, j] -> [0, 0]`之间的矩形的话
   - 根据`res`累加的性质，肯定需要返回`res[i+1][j+1] - 0`，我们需要一些0的位置。

2. 在计算`accumulate[i][j]`的时候，不是加上上面和左边的数就vans了的，还要减去左上角的数

   应该是`res[i][j] = nums[i][j] + res[i-1][j] + res[i][j-1] - res[i-1][j-1]`

   【脑子里想矩形的面积关系就行】

3. 在计算最终结果的时候，和`row2`相关的索引全是`row2+1`，和`row1`相关的索引不用加。为什么，可以根据前面那个303题推理，也可以给个特例想（特例就是从左上角`[0, 0]`开始到`[1, 1]`）

## 题解

```py
class NumMatrix(object):
    def __init__(self, matrix):
        m, n = len(matrix), len(matrix[0])
        self.res = [[0]*(n+1) for _ in range(m+1)]
        for i in range(1, m+1):
            for j in range(1, n+1):
                self.res[i][j] = matrix[i-1][j-1] + self.res[i-1][j] + self.res[i][j-1] - self.res[i-1][j-1]

    def sumRegion(self, row1, col1, row2, col2):
        return self.res[row2+1][col2+1] - self.res[row2+1][col1]-self.res[row1][col2+1]+self.res[row1][col1]

```



# 560. [Subarray sum equals k](https://leetcode.cn/problems/subarray-sum-equals-k/)

## 题意

给定整数数组`nums`，返回数组中和为`k`的连续子数组个数

## Example

```python
nums = [1, 1, 1, 1]
k = 2
res = 3
```

## 思路

这个题如果暴力解，需要两层for循环：

​	`for i in [0, n], for j in [i+1, n]: if sum(nums[i:j] == k): res += 1`

这样的时间复杂度直接飙到`O(n^3)`，实际上如果这道题能给出`O(n^2)`的算法，也是会超时的。

怎么达到`O(n)`，也就是一遍过呢？

- 想节省时间，hash绝对少不了
- 另一方面，我们需要把`sum(nums[i:j])`这种复杂度为`O(n)`的，给降到`O(1)`去，因此想到前缀和
  - 和为k，也即`accmu[i] - accmu[j] = k`

方法：

- 我们用一个数字`pre_sum`记录当前的前缀和，并用一个哈希表保存
- 当我们查到`pre_sum = x`时，我们去找hash表里面有多少个前缀和是`x-k`，加到`res`中即可。

## 题解

```python
class Solution(object):
    def subarraySum(self, nums, k):
        d = defaultdict(int)
        d[0] = 1
        pre_sum, res = 0
        for i in nums:
            pre_sum += i
            res += d.get(pre_sum-k, 0)
            d[pre_sum] += 1
        return res
```

