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

## 回溯(Backtracking) 

DFS的记录节点状态的深度优先搜索。

在搜索到某一节点的时候，如果我们发现目前的节点(及其子节点)并不是需求目标时，我们回退到原来的节点继续搜索，并且**<u>把在目前节点修改的状态还原</u>**。【这个在n皇后问题里面backtrack中体现的很明显】，也即，在回溯的步骤，变成了 ：

1. 修改当前节点状态
2. 递归子节点
3. 回改当前节点状态

使用情况：排列、组合、选择类问题

## 广度优先搜索(Breadth-first Search, BFS)

广度优先搜索是**一层层**地进行遍历，不追求先去找到一个叶节点（问题的解），而是先把每一步的所有情况都给整出来。

BFS的实现会用FIFO的Queue，一般是pop(0)，而不能直接pop()，记住这个即可。

# DFS 695. [Max Area of Island (Medium)](https://leetcode.cn/problems/max-area-of-island/)

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



# DFS 547. [Number of Provinces (Medium)](https://leetcode.cn/problems/number-of-provinces/)

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

跟上个题基本一致

## 题解

```python
class Solution(object):
    def findCircleNum(self, isConnected):
        if len(isConnected) == 1:
            return 1

        self.isConnected = isConnected
        self.maxlen = len(isConnected)
        self.res = 0
        for i in range(self.maxlen):
            if isConnected[i][i] == 1:
                self.res += 1
                self.dfs(i)
        return self.res
      
    def dfs(self, i):
        self.isConnected[i][i] = 0
        for j in range(self.maxlen):
            if self.isConnected[i][j] == 1:
                self.isConnected[j][i] = 0
                self.isConnected[i][j] = 0
                self.dfs(j)
```



# Backtrack 51. [N-Queens (Hard)](https://leetcode.cn/problems/n-queens/)

## 题意 / 示例

算法课讲了好多次了懒得写了

## 思路

- 用什么方式记录结果？

  - 因为每一行都会占一个皇后，所以res用**一维数组**记录就可以了
  - 比如[1, 3, 0, 2]，表示第0行的皇后在第1列，第1行的皇后在第3列，以此类推

- 如何排除掉不要的解？

  - 用三个set分别记录已经出现的列，已经出现的左对角线和右对角线

  - 对角线规律:

    - ![fig1](https://assets.leetcode-cn.com/solution-static/51/1.png)

    - ![fig2](https://assets.leetcode-cn.com/solution-static/51/2.png)

## 题解

```python
class Solution(object):
    def solveNQueens(self, n):
        def gen_borad():
            board = []
            row = ["."]*n
            for i in range(n):
                row[queens[i]] = "Q"
                board.append("".join(row))
                row[queens[i]] = "."
            return board
        def backtrack(row):
            if row == n:
                res.append(gen_borad())
            for i in range(n):
                if i in columns or (row - i) in diag1 or (row + i) in diag2:
                    continue
                queens[row] = i
                columns.add(i)
                diag1.add(row - i)
                diag2.add(row + i)
                backtrack(row + 1)
                columns.remove(i)
                diag1.remove(row - i)
                diag2.remove(row + i)
        res = []
        queens = [-1] * n
        columns, diag1, diag2 = set(), set(), set()
        backtrack(0)
        return res
```

# BFS 934. [Shortest Bridge [Medium]](https://leetcode.cn/problems/shortest-bridge/)

## 题意

- `grid:` 二元矩阵，大小为`n x n`，其中1表示陆地，0表示水域。
- 在`grid`中恰巧有两座岛，通过将任意数量的 `0` 变为 `1` ，以使两座岛连接起来，变成**一座岛** 。
- 求：必须翻转的0的最小数量。

## 示例

```python
grid = [[0,1,0],
        [0,0,0],
        [0,0,1]]
res = 2
```

## 思路

1. 首先去搜索一座岛，找到他的边缘 (DFS)
2. 对这座岛屿的边界进行扩展，每次向外扩1位(BFS)
3. 遇到第二座岛屿的时候，扩展的数量即为所求

## 题解

```python
class Solution(object):
    def shortestBridge(self, grid):

        def dfs(i, j):
            if i < 0 or j < 0 or i >= max_row or j >= max_col:
                return False
            if grid[i][j] == 1:
                grid[i][j] = 2
                edge_flag = False
                for m in range(4):
                    temp_res = dfs(i + direction[m], j + direction[m + 1])
                    edge_flag = edge_flag or temp_res
                if edge_flag:
                    edges.append((i,j))
            elif grid[i][j] == 0:
                return True
            return False

        direction = [-1, 0, 1, 0, -1]
        max_row = len(grid)
        max_col = len(grid[0])
        edges = []
        findFirstIsland = False
        for row in range(max_row):
            for col in range(max_col):
                if grid[row][col] == 1:
                    dfs(row, col)
                    findFirstIsland = True
                    break
            if findFirstIsland: 
                break
        
        def check_valid(aa, bb):
            if aa < 0 or bb < 0 or aa >= max_row or bb >= max_col:
                return False
            return True
        
        res = -1
        while True:
            edges_len = len(edges)
            res += 1
            for _ in range(edges_len):
                x, y = edges.pop(0)
                for n in range(4):
                    dx, dy = x + direction[n], y + direction[n+1]
                    if not check_valid(dx, dy):
                        continue
                    temp_val = grid[dx][dy]
                    if temp_val == 2: 
                        continue
                    elif temp_val == 0:
                        grid[dx][dy] = 2
                        edges.append((dx, dy))
                    else:
                        return res           
```

