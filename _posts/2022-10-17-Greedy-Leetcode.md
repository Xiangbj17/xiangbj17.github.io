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



# 435. [Non-overlapping Intervals (Medium)](https://leetcode.cn/problems/non-overlapping-intervals/)

## 题目大意

- `intervals:` 表示一些区间`[start, end]`的集合
- 有一些区间是重叠的，需要移掉一些使剩下的不重叠
- 返回 **需要移除区间的最小数量**

## 示例

```python
intervals = [[1,2],[2,3],[3,4],[1,3]]
res = 1 # 移除[1, 3]
```

## 思路

其实这个题和研一的时候上的算法课一个例题很像，好像是互不相容的工时什么什么的，忘了。但主要就是，当两个区间meet的时候，比如[1, 2]和[1, 3]，肯定优先选[1, 2]，因为这样留给后面的空间就越大，需要移除的区间就越少。

感觉贪心问题好像都得从特例比较去分析贪心策略。

贪心策略：

- 按照右边界排序，就从左向右遍历。策略是右边界越小越好，只要右边界越小，留给下一个区间的空间就越大。
- 按照左边界排序，就从右向左遍历。策略是左边界数值越大越好，这样就给前一个区间的空间就越大。

## 题解

```python
class Solution(object):
    def eraseOverlapIntervals(self, intervals):
        """
        :type intervals: List[List[int]]
        :rtype: int
        """
        if len(intervals) == 1:
            return 0
        intervals.sort(key=lambda x:x[1])
        res = 0
        right = intervals[0][1]
        for i in range(1, len(intervals)):
            if intervals[i][0] < right:
                # overlap case
                res += 1
            else:
                right = intervals[i][1]
        return res
```





# 452. [ Minimum Number of Arrows to Burst Balloons (Medium)](https://leetcode.cn/problems/minimum-number-of-arrows-to-burst-balloons/)

## 题目大意

- `points:` 是包含一些区间的集合
- 一个int可以同时被包含在不同的区间中，不如说这个数命中这些区间
- 返回能命中这些区间的最少的int数量

## 示例

```python
points = [[10,16],[2,8],[1,6],[7,12]]
res = 2 # 6在[2, 8]和[1, 6]中，8在[10, 16]和[7, 12]中
```

## 思路

和上一道题很像很像。可以通过这样的例子来思考贪心准则:

如果有[1, 2], [2, 3], [1, 4]，很简单能想到pick 2 来命中三个区间。但什么样的算法能找到2呢？

- 这个排序是根据右边升序，也就是越往后右边界越大。那么显然我选择当前的右边界，在后面更有胜算，因为右边界肯定被涵盖在后面的集合中，左边则不然 ==》选择当前的右边界做基准

- 如果后一个区间的左边界小于等于当前右边界，说明这个数对后一个区间也有效，那么保留这个数
- 如果后一个区间的右边界大于当前右边界，说明这个数对后面的区间无效了，res += 1

贪心准则：按照区间ending排序，选择当前区间右边界x作为基准，只要后面的区间包含当前区间的右边界，通通用x来命中这几个区间

## 题解

```python
class Solution(object):
    def findMinArrowShots(self, points):
        """
        :type points: List[List[int]]
        :rtype: int
        """
        if len(points) == 1:
            return 1
        points.sort(key=lambda x:x[1])
        res = 1
        right = points[0][1]
        for i in range(1, len(points)):
            if points[i][0] > right:
                res += 1
                right = points[i][1]
        return res
```

