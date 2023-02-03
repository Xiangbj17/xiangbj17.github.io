---
title: Python Leetcode101 利用数据结构(1):队列、栈和集合
date: 2023-01-30 19:37:00 +0800
categories: [Leetcode, Data Structure]
tags: [Algorithm]
render_with_liquid: false
---

# 辅助刷题的数据结构

[Python数据结构增强模块 Collections](https://www.cnblogs.com/goldsunshine/p/15760807.html)

# (I) 双端队列(deque)：一个非常强大的数据结构

既支持*O*(1)随机读取，又支持*O*(1)时间的头部增删和尾部增删，不过有一定的额外开销。

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

# (II) Stack | 栈 or 堆叠

栈是后入先出(LIFO)的数据结构，默认基于List实现，通常用于深度优先搜索，字符串匹配等问题

```python
>>> stack = [1, 2, 3, 4]
>>> stack.pop()
4
```

# (III) Queue | 队列

队列是先入先出(FIFO)的数据结构，默认基于List实现，通常用于广度优先搜索

```python
>>> queue = [1, 2, 3, 4]
>>> queue.pop(0)
1
```

# (IV) Priority Queue | 优先队列

[优先队列](https://geek-docs.com/python/python-examples/python-priority-queue.html)是优先值先出的数据结构，可以使用`queue.PriorityQueue`实现

```python
>>> from queue import PriorityQueue
>>> q = PriorityQueue()

# 使用q.put()方法放入元素
>>> q.put((2, 'two'))
>>> q.put((1, 'one'))
>>> q.put((3, 'three'))
>>> 
>>> q
<queue.PriorityQueue object at 0x7fcd84f16b90>

# 使用q.get()方法获取最优先的元素
>>> while not q.empty():
...     next_item = q.get()
...     print(next_item)

(1, 'one')
(2, 'two')
(3, 'three')
```

也可以使用heapq实现最小堆，从而实现优先队列

- heapq建立序列的两种方法

```python
>>> import heapq

# 第一种，使用heapq.heappush()一个一个加进去
>>> data = [5, 8, 6, 3, 4, 7, 0, 1, 2, 9]
>>> 
>>> heap = []
>>> for i in data:
...     heapq.heappush(heap, i)
>>> heap
[0, 1, 3, 2, 5, 7, 6, 8, 4, 9]

# 第二种，使用heapify()直接来整理数组
>>> data = [5, 8, 6, 3, 4, 7, 0, 1, 2, 9]
>>> heapq.heapify(data)
>>> data
[0, 1, 5, 2, 4, 7, 6, 3, 8, 9]
```

- heappop(heap)：将数组堆中的最小元素弹出

  ```python
  >>> heapq.heappop(data)
  0
  ```

- nlargest()和nsmallest()：用来寻找任何可迭代的对象iter中第n大或者第n小的元素

  ```python
  >>> heapq.nlargest(3, data)
  [9, 8, 7]
  
  >>> heapq.nsmallest(5, data)
  [1, 2, 3, 4, 5]
  ```

  

# (V) 集合 | Set

集合（set）是一个**无序的不重复**元素序列。

- 创建一个空集合：`set()`

- 用一些已有元素创建集合：

  - `x = {1, 2, 3, 1, 1, 1}` => 最终会有： `x = {1, 2, 3}`
  - 或者`m = [1, 2, 3, 1, 1, 1], x = set(m)`，效果同上

- 集合加元素或者减元素

  ```python
  >>> x = {1, 2, 3, 1, 1, 1}
  >>> x
  {1, 2, 3}
  
  # 添加一个元素,add()
  >>> x.add(0)
  >>> x
  {0, 1, 2, 3}
  
  # 添加多个元素，update()
  >>> x.update([6, 6, 6, 8])
  >>> x
  {0, 1, 2, 3, 6, 8}
  
  # 移除元素，remove(),不存在元素会报错
  >>> x.remove(8)
  >>> x
  {0, 1, 2, 3, 6}
  
  # 移除元素，discard()，不存在不会报错
  >>> x.discard(5)
  >>> x
  {0, 1, 2, 3, 6}
  ```

  

- 两个集合间的运算

```python
>>> a = set('abracadabra')
>>> b = set('alacazam')
>>> a                                  
{'a', 'r', 'b', 'c', 'd'}

# 集合a中包含而集合b中不包含的元素
>>> a - b                              
{'r', 'd', 'b'}

# 集合a或b中包含的所有元素
>>> a | b                              
{'a', 'c', 'r', 'd', 'b', 'm', 'z', 'l'}

# 集合a和b中都包含了的元素
>>> a & b                              
{'a', 'c'}

# 不同时包含于a和b的元素
>>> a ^ b                             
{'r', 'd', 'b', 'm', 'z', 'l'}
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

# 232. [Implement Queue Using Stacks (Easy)](https://leetcode.cn/problems/implement-queue-using-stacks/)

## 题目

仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（`push`、`pop`、`peek`、`empty`）

- void push(int x) 将元素 x 推到队列的末尾
- int pop() 从队列的开头移除并返回元素
- int peek() 返回队列开头的元素
- boolean empty() 如果队列为空，返回 true ；否则，返回 false

## Example

```python
# 输入:
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
# 输出:
[null, null, null, 1, 1, false]
```

## 思路

使用两个栈实现一个队列，其实很好想。首先肯定需要有一个栈来接收数据，直接用`append()`方法就行。

如果要实现队列的`popleft()`，就把接受栈的东西`pop()`全放进另一个，通过这种方法实现数据的倒置。

## 题解

```python
class MyQueue(object):
    def __init__(self):
        self.stackIn = []
        self.stackOut = []

    def push(self, x):
        self.stackIn.append(x)

    def pop(self):
        if not self.empty:
            return None

        if self.stackOut:
            return self.stackOut.pop()
        else:
            for _ in range(len(self.stackIn)):
                self.stackOut.append(self.stackIn.pop())
        return self.stackOut.pop()

    def peek(self):
        res = self.pop()
        self.stackOut.append(res)
        return res

    def empty(self):
        return not (self.stackIn or self.stackOut)

```



# 155. [Min Stack (Easy)](https://leetcode.cn/problems/min-stack/)

## 题目

设计一个`Minstack`类，在常数时间内检索到最小元素的栈，其函数包括：

- void push(int val) 将元素val推入堆栈。
- void pop() 删除堆栈顶部的元素。
- int top() 获取堆栈顶部的元素。
- int getMin() 获取堆栈中的最小元素。

## Example

```python
# 输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

# 输出：
[null,null,null,null,-3,null,0,-2]
```

## 思路

第一个方法是，用两个栈来实现，第一个栈存值，第二个栈存「到该位置为止」的最小值，一一对应，需要O(n)额外空间。也可以每次入栈两个元素，第一个元素记录新push进去的元素值，第二个元素记录当前的最小值，也是O(n)。

第二个方法，只需要O(1)的额外空间，但是代码写起来麻烦一些。

和之前有一道题的思路一样，用一个位置同时存当前push()进去的值和当前的最小值。

| 当前操作 | push(-2) | push(0) | push(-3)    | pop()   | getMin() |
| -------- | -------- | ------- | ----------- | ------- | -------- |
| Stack    | [-2]     | [-2, 0] | [-2, 0, -3] | [-2, 0] | [-2, 0]  |
| 最小值   | -2       | -2      | -3          | -3      | -2       |

除了stack的第一个元素，其余元素push进来的时候都存成「元素的值 - 上一个最小值」，这样的话，可以得到：

| 当前操作  | push(-2) | push(0) | push(-3)    | pop()   | getMin() |
| --------- | -------- | ------- | ----------- | ------- | -------- |
| New_stack | [-2]     | [-2, 0] | [-2, 0, -1] | [-2, 0] | [-2, 0]  |
| Temp_min  | -2       | -2      | -3          | -3      | -2       |

这样，当我们发现当pop出来的是一个负数的时候，就能说明，在这一步，`temp_min`进行了更新，且之前的`temp_ min`就是当前当前的`temp_min - pop`值。

## 题解

```python
class MinStack(object):
    def __init__(self):
        self.stack = []
        self.min = float('inf')

    def push(self, val):
        self.stack.append(val-self.min) # append当前的val-之前的min
        self.min = min(self.min, val)

    def pop(self):
        # 删除顶部元素，所以同时需要对min进行一个修改
        temp = self.stack.pop()
        if temp < 0:
            self.min -= temp

    def top(self):
        if not self.stack:
            return 
        num = self.stack[-1]
        if num < 0:
            return self.min
        else:
            return self.min + num

    def getMin(self):
        return self.min

```



# 20. [Valid Parentheses (Easy)](https://leetcode.cn/problems/valid-parentheses/)

## 题目

给定一个只包含`(, ), [, ], {, }`的字符串，判断其是否有效

## Example

```python
s = "()[]{}"
res = True
s = '(]'
res = False
```

## 思路

这种题套路就是用栈来做，把左括号压进栈里，遇到同类型右括号就拿出来

如果栈顶端的左括号和当前的右括号不匹配，返回`False`

若遍历完成之后栈里无剩余元素，那么就是`True`

## 题解

```python
class Solution(object):
    def isValid(self, s):
        stack = []
        dic = {')':'(', ']':'[', '}':'{'}
        for c in s:
            if c not in dic.keys():
                stack.append(c)
            else:
                if not stack:
                    return False
                temp = stack.pop()
                if temp != dic[c]:
                    return False
        if not stack:
            return True
        return False
```

# 单调栈

单调栈通过维持栈内值的单调递增或递减特性，在O(n)时间内处理需要比较大小的问题。

最常见的就是「找元素右边第一个大于其的元素」这类问题。

# 739. [Daily Temperatures (Medium)](https://leetcode.cn/problems/daily-temperatures/)

## 题目

`temperatures`是一个记录每日温度的数组，返回一个数组`answer`，其中：

`answer[i]`表示对于第`i`天，下一个更高温度出现在第几天后。若今后气温都低于该天，则为`0`

## Example

```python
temperatures = [73,74,75,71,69,72,76,73]
res = [1,1,4,2,1,1,0,0]
```

## 思路

这是一个还没遇到过的解题思路，总体想法是这样：

- 建一个栈，用来存储还没能找到「在他后面且比他大的温度」的位置，一开始为空
- 从左向右遍历`temperatures`数组，假设当前的位置和温度分别为`(i, t)`
  - 如果栈为空，直接将`(i, t)`入栈，`continue`
  - 如果栈非空，我们从栈顶开始，一个个对比栈内元素`pre_t`和当前`t`的大小：
    - 如果小于`t`，说明这个`i`是`pre_t`对应日期之后第一个比他大的日期：
      - 很高兴，我们为`pre_i`这个位置找到了解，有`res[pre_i] = i - pre_i`
      - 然后，继续看栈里面还有没有比`t`小的元素
    - 如果大于或者等于`t`，说明栈里面的元素没有比当前元素更小的了
      - 那么我们把`(i, t)`入栈
- 遍历完之后，如果栈里面还有元素，说明这些元素后面没有比它更高的温度了，直接设为`0`

可以看看这个[视频](https://leetcode.cn/problems/daily-temperatures/solution/leetcode-tu-jie-739mei-ri-wen-du-by-misterbooo/)，很清晰

## 题解

```python
class Solution(object):
    def dailyTemperatures(self, temperatures):
        res = [0] * len(temperatures)
        stack = []
        for i, t in enumerate(temperatures):
            while stack and t > temperatures[stack[-1]]:
                # 只要stack不为空，且当前温度大于栈顶温度，就一直操作
                pre_i = stack.pop()
                res[pre_i] = i - pre_i
            stack.append(i)
        return res
```

# 优先队列

优先队列(priority queue) 可以在*O*(1) 时间内获得最大值，并且可以在 *O*(log *n*) 时间内取出最大值或插入任意值。

优先队列常常用[堆(heap)](https://www.youtube.com/watch?v=j-DqQcNPGbE)来实现。堆是一个完全二叉树，其每个节点的值总是大于等于子节点的值。

实际实现堆时，我们通常用一个数组而不是用指针建立一个树。这是因为堆是完全二叉树，所以用数组表示时，位置 `i` 的节点的父节点位置一定为 `(i-1) // 2`，而它的两个子节点的位置又一定分别为 `2i+1` 和 `2i+2`

# 23. [Merge k Sorted Lists (Hard)](https://leetcode.cn/problems/merge-k-sorted-lists/)

## 题目

给定`k`个增序的链表，把它们合并成一条链表

## Example

```python
lists = [[1,4,5],[1,3,4],[2,6]]
res = [1,1,2,3,4,4,5,6]
```

## 思路

这一题，其实只要知道“优先队列”是一个提示，做起来就很简单了。

我感觉这个题难度可以放Medium甚至Easy，因为我这种人基本没啥问题都能一下写出来。

其实就是把所有链表的头都放在最小堆里面，每次取最小的那个头加在最终的链表里面，就行。

## 题解

```python
class Solution(object):
    def mergeKLists(self, lists):
        import heapq
        # 初始化pointers
        pointers = []
        for i in lists:
            if not i:
                continue
            heapq.heappush(pointers, (i.val, i))
        # pointers = [(1, node1), (1, node2)...]
        head = ListNode() # 创建一个假头结点
        last = head
        while pointers:
            _, temp_node = heapq.heappop(pointers)
            last.next = temp_node
            if temp_node.next:
                heapq.heappush(pointers, (temp_node.next.val, temp_node.next))
            last = temp_node
        return head.next
```

# 218. [The Skyline Problem (Hard)](https://leetcode.cn/problems/the-skyline-problem/)

这个题太难了，建议温习的时候直接看这个[视频](https://www.youtube.com/watch?v=GSBLe8cKu0s)

## 题解

```python
from sortedcontainers import SortedDict

class Solution(object):
    def getSkyline(self, buildings):
        """
        我们需要的sort条件:
        1. 如果左端点相同，queue里优先放更高的
        2. 如果右端点相同，queue里优先放更低的
        3. 如果一个左端点和一个右端点重合，先处理右端点，再加入左端点
        """
        # 首先，把所有的点左右端点拿出来排个序
        records = []
        for left, right, height in buildings:
            # 如果是左端点，取负作为标记,这样排序的时候高楼会在前。条件1达成。
            # 如果是左端点，取正作为标记，这样最小堆排序，低楼在前。条件2达成。
            heapq.heappush(records, (left, -height)) 
            heapq.heappush(records, (right, height))
        
        # 为了解决条件3，使用一个SortedDict()维护不同高度的建筑数量
        # 高度为地平线的建筑始终至少有1个(可以理解为从0到inf有个高度为0的建筑)
        lives = SortedDict()
        lives[0] = 1

        res = []

        while records:
            # 取出当前第一个点
            x, h = heapq.heappop(records)
            # 分左点（入点）和右点（出点）讨论
            if h < 0:
                if h in lives:
                    lives[h] += 1
                else:
                    lives[h] = 1
                    # 如果当前值h是dict.value中的最大值，说明是最高
                    if h == lives.keys()[0]:
                        res.append([x, -h])
            else:
                lives[-h] -= 1
                if not lives[-h]:
                    # 先把key从dict中移除
                    lives.pop(-h)
                    temp_max = lives.keys()[0]
                    # 如果最高值变小了，说明这个地方出现了一个落点
                    if -temp_max < h:
                        res.append([x, -temp_max])
        return res
```

# 双端队列

# 239. [Sliding Window Maxmun (Hard)](https://leetcode.cn/problems/sliding-window-maximum/)

```python
class Solution:
    def maxSlidingWindow(self, nums, k):
        # 如果数组为空或 k = 0，直接返回空
        if not nums or not k:
            return []
        # 如果数组只有1个元素，直接返回该元素
        if len(nums) == 1:
            return [nums[0]]

        # 初始化队列和结果，队列存储数组的下标
        queue = []
        res = []

        for i in range(len(nums)):
            # 如果当前队列最左侧存储的下标等于 i-k 的值，代表目前队列已满。
            # 但是新元素需要进来，所以列表最左侧的下标出队列
            if queue and queue[0] == i - k:
                queue.pop(0)

            # 对于新进入的元素，如果队列前面的数比它小，那么前面的都出队列
            while queue and nums[queue[-1]] < nums[i]:
                queue.pop()
            # 新元素入队列
            queue.append(i)

            # 当前的大值加入到结果数组中
            if i >= k-1:
                res.append(nums[queue[0]])
        return res
```

