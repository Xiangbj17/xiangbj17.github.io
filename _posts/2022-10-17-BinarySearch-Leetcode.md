---
title: Python Leetcode101 二分查找
date: 2022-10-25 10:21:00 +0800
categories: [Leetcode, BinarySearch]
tags: [Algorithm]
render_with_liquid: false
---

# 二分查找

- 规则：每次将待查找区间分成两部分，排除一半，从另一半继续查找
- 复杂度：对于一个长度为 *n* 的数组，二分查找的时间复杂度为 *O*(log *n*)
- Tips：
  - 熟练使用一种写法，比如左闭右开(满足 C++、Python 等语言的习惯)或左闭右闭(便于处理边界条件)，尽量只保持这一种写法
  - 在刷题时思考如果最后区间只剩下一个数或者两个数，自己的写 法是否会陷入死循环，如果某种写法无法跳出死循环，则考虑尝试另一种写法。

# 34. [Find First and Last Position of Element in Sorted Array (Medium)](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

## 题意

- `nums`: 按照非递减排列的整数数组
- `target`: 目标值
- 找出给定目标值在数组中的开始位置和结束位置，不存在则返回[-1, -1]
- 必须设计时间复杂度为O(log n)的算法解决问题【赤裸裸的提示】

## 示例

```python
# Example 1
nums = [5,7,7,8,8,10], target = 8
res = [3, 4]

# Example 2
nums = [], target = 0
res = [-1, -1]
```

## 思路

这道题之前竟然做过而且一次通过，真是牛逼，但我这次碰上就已经忘记了淦。

重新整理一下思路。

虽然题目已经提示了二分搜索，但还有几个比较重要的点：

1. **全程使用二分搜索**，不能突然改成遍历，不然复杂度就变了。比如，如果我想「用简单的二分搜索随便找到一个target的位置，然后往左遍历找到第一个出现的位置，再往右遍历找最后一个」，这是不可以的
2. 根据上一条，左边界和右边界都必须通过二分搜索出来。那也就是说「我必须设计一个二分算法，使之能**精确找到左边界或者右边界的位置**，而不是位于其他位置的target」
3. 另外就是说，找到一个边界之后，比如我先找到左边界，其实另一个边界可以**在[lower_bound, len(nums)-1]之间搜索**，节省一下时间。

上面这些是大方向，但具体来说，如何精确找到某一个边界，又需要进行设计。

以左边界为例，

- 当每找到一个`target`的时候，不管是不是左边界，先更新记录左边界的 global variable **`start`**
- 循环继续往下找，找到更小的就覆盖之前的`start`值，直到指针指向比`target`更小的数为止。

代码肯定是要沿用二分的骨架，一些条件需要改：

- Case  `nums[mid] == target`: 更新`start`，同时右指针跑到中间指针左边
- Case  `nums[mid] < target`: 说明target在右边，左指针跑到中指针右边
- Case  `nums[mid] > target`: 说明target在左边，右指针跑到中指针左边

注意这个循环的条件是`left <= right`，因为如果当left和right都刚好位于lower_bound的时候，还得计算一次。这时候中间指针也来到lower_bound，把这个更新到`start`去，不然就找不到了。

## 题解

```python
class Solution(object):
    def searchRange(self, nums, target):
        # First find the start target
        left, right, start = 0, len(nums)-1, -1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target:
                start = mid
                right = mid - 1
            elif nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        if start == -1: return [-1, -1]
        # Then the end target between start and len(nums) - 1
        left, right, end = start, len(nums)-1, -1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target:
                end = mid
                left = mid + 1
            else:
                right = mid - 1
        return [start, end]
```











