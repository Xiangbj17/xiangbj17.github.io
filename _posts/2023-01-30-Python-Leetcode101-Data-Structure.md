---
title: Python Leetcode101 利用数据结构
date: 2023-01-30 19:37:00 +0800
categories: [Leetcode, Data Structure]
tags: [Algorithm]
render_with_liquid: false
---

# 辅助刷题的数据结构

[Python数据结构增强模块 Collections](https://www.cnblogs.com/goldsunshine/p/15760807.html)

## 1. Sequence Container | 维持顺序的容器

- [顺序表(list)](https://jackkuo666.github.io/Data_Structure_with_Python_book/chapter2/section4.html)：元素个数可变的线性表，可以增删元素，并在各种操作中保序

- 元祖(tuple)：大小固定，一般刷题不使用

- 单向链表：刷题一般不使用（但会出题，后面得分专题刷）

- 双向链表：刷题一般也不使用（但也会出题，同上）

- 双端队列(deque)：一个非常强大的数据结构，既支持*O*(1)随机读取，又支持*O*(1)时间的头部增删和尾部增删，不过有一定的额外开销。

  ```python
  """deque的常见用法，其实相比list就多了popleft和appendleft"""
  >>> from collections import deque
  
  # 创建一个deque
  >>> deck = deque([1, 2, 3])  
  >>> deck
  deque([1, 2, 3])
  
  # 在最左边插入一个元素
  >>> deck.appendleft(0)
  >>> deck
  deque([0, 1, 2, 3])
  
  # 在最右边插入一个元素
  >>> deck.append(4)
  >>> deck
  deque([0, 1, 2, 3, 4])
  
  # 拿出最右边的元素
  >>> deck.pop()
  4
  
  # 拿出最左边的元素
  >>> deck.popleft()
  0
  
  # 查询某元素出现的个数
  >>> deck.count(1)
  1
  
  # 在左边加入一些元素,注意这个顺序是倒过来的
  >>> deck.extendleft([-2, -1, 0])
  >>> deck
  deque([0, -1, -2, 1, 2, 3])
  
  # 删除找到的第一个value，没有则引发ValueError
  >>> deck.remove(0)
  >>> deck
  deque([-1, -2, 1, 2, 3])
  
  ```

# 数组和矩阵相关问题

# 448. [Find All Numbers Disappeared in an Array (Easy)](https://leetcode.cn/problems/find-all-numbers-disappeared-in-an-array/)

## 题目

数组`nums`包含`n`个整数，其所有的元素都在区间`[1, n]`内。

返回该区间内没有出现在nums中的数字，以数组的形式返回。

## Example

```python
nums = [1, 1]
res = 2  # n = 2， 然而只出现了1
```

## 思路

对nums进行一次遍历，如果搜索到某一元素，就对该元素对应的位置加一个n

这样，对nums进行第二遍遍历的时候，如果某位置的值小于等于n，这个数就没出现过

要注意的是，在第一次遍历的时候，很有可能遍历到某个数的时候，这个数值已经被加过了，只需要对n求一个余数就能得到这个数的还原。

## 题解

```python
class Solution(object):
  	# 时间复杂度:O(n), 空间复杂度O(1),返回值不计入空间复杂度
    def findDisappearedNumbers(self, nums):
        n = len(nums)
        for num in nums:
            original_num = num % n
            nums[original_num-1] += n
        res = []
        for i in range(n):
            if nums[i] <= n:
                res.append(i+1)
        
        return res
```



# 48. [Rotate Image (Medium)](https://leetcode.cn/problems/rotate-image/)

## 题目

`matrix`是一个`n * n`的二维矩阵，表示一个图像

要求：将`matrix`顺时针旋转90°，原位修改，不需要返回

## Example

<img src="https://assets.leetcode.com/uploads/2020/08/28/mat2.jpg" alt="img" style="zoom:50%;" />

```python
matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
res = [[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

## 思路

考虑：如果我们每次旋转一个位置，那么最少需要动和它有关的三个数。不然转的过程中，就会丢失数据。

在上面的例子中，每一次旋转至少需要动的位置用相同的颜色标出来了：

<img src="https://user-images.githubusercontent.com/84035000/215794260-8563abd2-f2cd-4ffc-898b-d5851c2695b8.png" alt="image" style="zoom: 50%;" />

这些数之间的关系可以通过几何关系推导出来，如下图所示，在推导的过程中，可以这么想：

- 我们先找到`[i, j]`与「需要挪到`[i, j]`那个地方的」数的坐标之间的关系
  - 在下面的例子中，很明显可以感受出来是14:`[3(x), 1(y)]`要挪到2:`[1(i), 0(j)]`那个地方去
  - 从几何关系上来看，14在第二列，2在第二行，那么`y = i`可以确定
  - 2在第一列，14在第四行，、能确定一个`x = n -j -1`这样一个关系
- 根据上面的分析，我们知道`[i,j] <- [n-j-1, i]`
- 那么， `[n-j-1, i]`对应的是谁呢？
  - 很简单，我们把`i = n-j-1, j = i`代入进「`[i,j] <- [n-j-1, i]`」这样一个关系式中，有：
  - `[n-j-1, i] <- [n-i-1, n-j-1]`
- 继续代入，有` [n-i-1, n-j-1] <- [n-(n-j-1)-1, n-i-1] = [j, n-i-1]`

<img src="https://user-images.githubusercontent.com/84035000/215794556-0aff14b3-b26c-4671-8e8f-40baae648671.png" alt="截屏2023-01-31 22 05 18" style="zoom:45%;" />

接下来，还需要考虑的是，循环的条件怎么进行设计：不难发现，如果在`n=4`的情况下，

- 第一行，我们只需要动`1, 2, 3`三个位置
- 第二行，我们只需要动`6`这一个个位置

其余地方，循环都已经走过了。那么`i in range(n//2), j in range(i, n-i-1)`

## 题解

```python
class Solution(object):
    def rotate(self, matrix):
        # 如果len(matrix)是奇数，最中心的点不需要动，偶数则也需要
        n = len(matrix)
        for i in range(n//2):
            for j in range(i, n-i-1):
                temp = matrix[i][j]
                matrix[i][j] = matrix[n-j-1][i]
                matrix[n-j-1][i] = matrix[n-i-1][n-j-1]
                matrix[n-i-1][n-j-1] = matrix[j][n-i-1]
                matrix[j][n-i-1] = temp
```

## 另外的解法

还有一种方法，旋转90度可以看做是先转置，再把每一行倒过来，这样的话，代码写起来就快很多

```python
class Solution(object):
    def rotate(self, matrix):
        for i in range(0, len(matrix)):
            for j in range(i, len(matrix)):
                temp = matrix[i][j]
                matrix[i][j] = matrix[j][i]
                matrix[j][i] = temp
        for i in range(0, len(matrix)):
            matrix[i] = matrix[i][::-1]
```



# 240. [Search a 2-D Metrix (Medium)](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

## 题目

给定一个`m * n`的二维矩阵matrix，矩阵按规律排列：每行元素从左到右升序，每列从上到下升序

编写一个高效的算法来搜索一个目标值 target 在不在矩阵中， 只需要返回`True`或者`False`

## Example

```python
matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]]
target = 20
res = False
```

## 思路

这里有一个技巧，可以从右上角开始找。

因为元素从左到右升序，说明左边的数总是小于右边；从上到下升序，说明下面的数总是大于上面。

那么，从右上角开始，如果`target`比当前的数小，往左找，如果大则往右找，如果到左下角还没找到，说明不在

## 题解

```python
class Solution(object):
    def searchMatrix(self, matrix, target):
        i, j = 0, len(matrix[0])-1
        while i <= len(matrix)-1 and j >= 0:
            if matrix[i][j] == target:
                return True
            elif matrix[i][j] > target:
                j -= 1
            else:
                i += 1
        return False
```



# 769. [Max chunks to make sorted (Medium)](https://leetcode.cn/problems/max-chunks-to-make-sorted/)

## 题目

给定一个含有 0 到 *n* 整数的数组，每个整数只出现一次

求这个数组最多可以分割成多少个子数组：使得对每个子数组进行增序排序后，原数组也是增序的

## Example

```python
arr = [1, 0, 2, 3, 4]
res = 4
# 可以分成[1, 0], [2], [3], [4]，增序排序后就是[0, 1, 2, 3, 4]

arr = [4, 3, 2, 1, 0]
res = 1
# 没办法，只能整个送进去排序，不然没法增序
```

## 思路

从左往右遍历，同时记录当前的最大值，每当当前最大值等于数组位置时，增加一次分割。原理：

- 如果当前最大值大于数组位置`i`，则说明右边一定有小于`i`的数字，需要把它也加入待排序的子数组
- 因为数组只包含不重复的 0 到 *n*，所以当前最大值一定不会小于数组总长度`n+2`
- 所以每当当前最大值等于数组位置时，假设为 *p*，我们可以成功完成一次分割，并且其与上一次分割位置 *q* 之间的值一定是 *q* + 1 到 *p* 的所有数字。

## 题解

```python
class Solution(object):
    def maxChunksToSorted(self, arr):
        res, cur_max = 0, 0
        for i in range(len(arr)):
            cur_max = max(arr[i], cur_max)
            if cur_max == i:
                res += 1
        return res
```

# 栈和队列
