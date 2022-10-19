---
title: Python Leetcode101 双指针
date: 2022-10-17 21:08:00 +0800
categories: [Leetcode, DoublePointer]
tags: [Algorithm]
render_with_liquid: false
---

# 关于双指针

双指针主要用于**遍历数组**，两个指针指向不同的元素，从而协同完成任务。

也可以延伸到多个数组的多个指针。

若两个指针指向同一数组：

- 遍历方向相同且不会相交，则也称为<u>滑动窗口</u>(两个指针包围的区域即为当前的窗口)，经常用于区间搜索。

- 遍历方向相反，则可以用来进行<u>搜索</u>，待搜索的数组往往是 排好序的。

# 167. [Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

## 题目大意

- `numbers`: 是一个【下标从1开始的】整数数组，按非递减顺序排列
- 找出满足和等于 `target` 的两个数
- 以长度为2的数组`[index1, index2]`返回这两个数的下标

## 示例

```python
numbers = [2, 7, 11, 15]
target = 9
res = [1, 2] # 2和7, 下标分别为1和2
```

## 思路

【这种题啊，是有套路的，记住这个套路就行，以后可以往别的题里面套】

方法：用方向相反的双指针来寻找这两个数字

- left 从最小的数开始，从左往右搜索
- right 从最大的数开始，从右往左搜索
- 若`nums[left]+nums[right] = target`，返回结果
- 若两个指针指向元素的和小于给定值，left+=1，使得当前和变大
- 若两个指针指向元素的和小于给定值，right-=1，使得当前和减小

**可以<font color=green>证明</font>**，对于排好序且有解的数组，双指针一定能遍历到最优解。

我觉得Leetcode评论区的一个留言的说明更加直观：

<img src="https://pic.leetcode-cn.com/6ee3750f6036a7a6249197e5b640bfc0564153ca1a61c1e35aad51f3a8f9dc5e.jpg" alt="下标 i, j 的搜索空间" style="zoom:50%;" />

- 原本的搜索空间是$O(n^2)$级别，搜索空间是**<u>白色的倒三角</u>**部分
- 一开始计算`nums[0]+nums[7]`
  - 如果它小于target，左指针右移：这一步隐含的意思是，最大的数和第一个数加起来都比target小了，那其他的数和第一个加起来不更小？于是只能把左指针往右移。其实就排除掉了第一行的白色区域。
  - 如果他大于target，右指针左移：这一步隐含的意思是，最小的数和最后一个数加起来都比target大，那其他的数和最后的数加起来不更大了？于是只能把右指针左移，这一步实际上排除掉了第七列的内容。
- 就这样每次消除一行或者一列白色，直到最后获得解。
- 所以可以把时间复杂度降低到$O(n)$级别

## 题解

```python
class Solution(object):
    def twoSum(self, numbers, target):
        left, right = 0, len(numbers)-1
        while left < right:
            summation = numbers[left] + numbers[right]
            if summation < target:
                left += 1
            elif summation > target:
                right -= 1
            else:
                return left+1, right+1
```

## 





