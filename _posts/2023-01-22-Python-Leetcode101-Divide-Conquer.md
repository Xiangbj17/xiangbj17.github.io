---
title: Python Leetcode101 化繁为简分治法
date: 2023-01-22 13:56:00 +0800
categories: [Leetcode, Divide Conquer]
tags: [Algorithm]
render_with_liquid: false
---

# 分治问题的数学表达

先考虑归并排序。归并排序就是每次将长度砍一半，分好那一半，再合并到一起。

定义 *T*(*n*) 表示处理一个长度为 n 的数组的时间复杂度，则归并排序的时间复杂度递推公式为 *T* (*n*) = 2*T* (*n*/2) + *O*(*n*)。其中：

- 2*T* (*n*/2) 表示我们分成了两个长度减半的子问题
- *O*(*n*) 则为合并两个长度为 *n*/2 数组的时间复杂度。

根据主定理，可以得到最终的时间复杂度。

## 主定理

![image](https://user-images.githubusercontent.com/84035000/213903112-4cc5f4b3-c40c-4d76-9277-c1e03b889cc4.png)

## 对主定理的理解

可以想象成`T(n) = aT(n/b) + f(n)`中，前者的复杂度是`O[n^logb(a)]`，如果`f(n)`的复杂度：

- 小于`O[n^logb(a)]`：复杂度以`aT(n/b)`为准，`T(n) = O[n^logb(a)]`
- 等于`O[n^logb(a) * log(n)^c ]`：复杂度等于`T(n) = O[n^logb(a) * (log(n))^(c+1) ]`
- 大于`O[n^logb(a)]`：复杂度以`f(n)`为准，有`T(n) = O(f(n))`

## 归并排序

归并排序有：`a = b = 2`，那么参考复杂度为`O(n)`，而`f(n) = n = n * log(n)^0`

满足上面的第二条，所以`T(n) = O(nlogn)`



# 表达式问题

# 241. [Different Ways to Add Parentheses (Medium)](https://leetcode.cn/problems/different-ways-to-add-parentheses/)

## 题目

- 字符串`expression`由数字和运算符`"+", "-", "*"`组成
- 按照不同的优先级组合这些运算，返回所有有可能的结果

## Example

```python
experssion = "2-1-1"
res = [0, 2] # (2-1)-1=0, 2-(1-1) = 2
```

## 思路

Divide-Conquer和DFS的不同之处在于，后者需要定义并调用一个`dfs()`函数，进行深度搜索；而前者是调用自己，并且就在自己的函数内部进行Conquner。这一点在下面的代码里面体现的很明显。

既然我们知道了要调用自己，那么子问题返回的数据类型应该和我们要解决的总问题是一致的。也就是说，子问题返回的应该是「子问题的所有可能解」，类型应该是`list`。

### 从Example看算法步骤

对于上面的Example：`2-1-1`，计算过程如下：

- 搜索到第一个减号
  - 左边是个int，2，直接返回一个`left_res = [2]`
  - 右边是一个算式`1-1`，这个又会进入一次嵌套调用，又搜索到一个减号
    - 左边返回`right_left_res = [1]`
    - 右边返回`right_right_res = [1]`
    - 两个拼凑起来，会得到一个`[0]`
  - `left_res - right_res = [2]`
- 搜索到第二个减号
  - 左边是`2-1`，返回一个`left_res = [1]`
  - 右边是一个int，1，返回`right_res = [1]`
  - 两个拼凑起来，`left_res - right_res = [0]`
- 最终的结果`res = [2, 0]`

### 对一般步骤的推导

那首先，我们需要用一个for循环去搜索到所有的运算符号的位置

再以这个运算符号为分割，去计算他左边和右边的所有可能结果

最后，把左边和右边的结果都整合起来



## 题解

```python
class Solution(object):
    def diffWaysToCompute(self, expression):
        res = []
        ops = {'+':lambda x,y:x+y, '-':lambda x,y:x-y, '*':lambda x,y:x*y}
        for indx in range(1,len(expression)-1): # 不用搜索第一个和最后一个字符因为肯定是数字
            if expression[indx] in ops.keys():
                # 搜索到运算符
                for left in self.diffWaysToCompute(expression[:indx]):
                    for right in self.diffWaysToCompute(expression[indx+1:]):
                        # 上面的两个for是在divide
                        # 下面这一句是在conquer，把左右分别的res整合成一个总的res
                        res.append(ops[expression[indx]](left,right))                      
        if not res:
            # 如果没有搜到任何一个运算符，说明expression已经收缩到一个int
            res.append(int(expression))
        # 返回一个list
        return res
```





|      | ""   | e    | ea   | eat  |
| ---- | ---- | ---- | ---- | ---- |
| ""   | 0    | 1    | 2    | 3    |
| s    | 1    | 2    | 3    | 4    |
| se   | 2    | 1    | 2    | 3    |
| sea  | 3    | 2    |      |      |

