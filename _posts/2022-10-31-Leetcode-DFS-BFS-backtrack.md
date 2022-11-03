---
title: Python Leetcode101 搜索算法
date: 2022-10-31 21:28:00 +0800
categories: [Leetcode, Search]
tags: [Algorithm]
render_with_liquid: false
---

# 本章算法

## 深度优先搜索(Depth-first Search, DFS)

- 在搜索到一个新的节点时，立即对该新节点进行延伸直到完成任务，得到一个解
- 通常会用【先入后出】的栈来实现，或者用与其等价的递归来实现。

P.S. 

- DFS可以用来检测环路:
  - 记录每个遍历过的节点的父节点，若一个节点被再次遍历且父节点不同，则说明有环
- 有时我们可能会需要对已经搜索过的节点进行标记，以防止在遍历时重复搜索某个节点

## 回溯(backtracking) 

DFS的录节点状态的深度优先搜索

## 广度优先搜索(Breadth-first Search, BFS)



# 695. [Max Area of Island (Medium)](https://leetcode.cn/problems/max-area-of-island/)

## 题意

- 有一个二维的二进制矩阵`grid`
- 求最多的相连的1的个数

## 示例

<img src="https://assets.leetcode.com/uploads/2021/05/01/maxarea1-grid.jpg" alt="img" style="zoom:50%;" />

```python
grid = [[0,0,1,0,0,0,0,1,0,0,0,0,0],[0,0,0,0,0,0,0,1,1,1,0,0,0],[0,1,1,0,1,0,0,0,0,0,0,0,0],[0,1,0,0,1,1,0,0,1,0,1,0,0],[0,1,0,0,1,1,0,0,1,1,1,0,0],[0,0,0,0,0,0,0,0,0,0,1,0,0],[0,0,0,0,0,0,0,1,1,1,0,0,0],[0,0,0,0,0,0,0,1,1,0,0,0,0]]
res = 6

```

## 思路

1. 深度优先搜索肯定是有的
2. 如果逮到一个1，赶紧搜它旁边还有多少1，搜过之后就把1改成0，防止之后再被搜到，记录这次搜索到的1的个数
3. 要注意的地方主要是一些边界条件，比如如果这个`(i,j)`抵达了边界，不能继续往下走了，需要kill这个节点，提前return

## 题解

```python
class Solution(object):
    def maxAreaOfIsland(self, grid):
        self.grid = grid
        self.max_i, self.max_j = len(grid), len(grid[0])
        self.temp_res = 0
        res = 0
        for i in range(self.max_i):
            for j in range(self.max_j):
                if grid[i][j] == 0: continue
                else:
                    self.temp_res = 0
                    self.dfs(i, j)
                    if self.temp_res > res:
                        res = self.temp_res
        return res

    def dfs(self, i, j):
        if i < 0 or i >= self.max_i or j < 0 or j >= self.max_j:
            return
        if self.grid[i][j] == 0:
            return
        self.temp_res += 1
        self.grid[i][j] = 0
        for di, dj in [[1, 0], [-1, 0], [0, 1], [0, -1]]:
            self.dfs(i+di, j+dj)
        return

```



# 547. [Number of Provinces (Medium)](https://leetcode.cn/problems/number-of-provinces/)

## 题意

- `isConnected`: 给出了节点之间的联系，如果节点之间是连接的，就是1，不连接则是0
- 有联系的节点形成一个圈子，也有点和别人都没联系，自己形成一个圈子
- 返回`isConnected`中圈子的数量

## 示例

![img](https://assets.leetcode.com/uploads/2020/12/24/graph1.jpg)

```python
isConnected = [[1,1,0],[1,1,0],[0,0,1]]
res = 2
```

## 思路

