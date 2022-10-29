---
title: Python Leetcode101 排序算法
date: 2022-10-29 21:20:00 +0800
categories: [Leetcode, Sort]
tags: [Algorithm]
render_with_liquid: false
---

# Quicksort 快速排序

[YouTube QuickSort in 4 min](https://www.youtube.com/watch?v=Hoixgm4-P4M)

## 关键词

1. Divide And Conquer
2. Pivot

## 思路

### 本思路根据上面的YouTube视频总结

1. 选择一个数作为Pivot

2. 使得Pivot的左边都是小于他的数，右边都是大于他的数，具体操作如下【这个过程叫做partition】：

   - 把Pivot和数组的最右元素交换，也即

     ```python
     [x, x, x, x, pivot, x, x, x, z] -> [x, x, x, x, z, x, x, x, pivot]
     ```

   - 两个指针left和right，left从左边第一个数开始往右走，right从倒数第二个数开始往左走
   - while left < right:
     - 如果left指向的元素小于pivot，left往右走，如果大于，则停下；
     - 如果right指向的元素大于pivot，right往左走，遇到小于的，停下；
     - 交换left和right指向的元素，两个指针继续往各自的方向走
   - 循环结束后，交换pivot和left

3. 继续选择其他的pivot并重复上述过程，直到整个array排好序

   

### 一个特殊的例子如下，经过一次交换，可以直接Sort整个Array

```python
original = [1, 6, 5, 3, 7]
# Choose 5 as pivot, move it to the right, i.e. change it with 7
-> [1, 6, 7, 3, 5]
# First Swap: Left at 6, right at 3
-> [1, 3, 7, 6, 5]
# After the swap, left = right at element 7, break the loop, change left with pivot 5
-> [1, 3, 5, 6, 7]
```

## 不同版本

快速排序在选择pivot的方式上有不同的版本，我感觉会写一种就行。

1. 随机
2. 总是选择第一个元素作为pivot
3. 总是选择最后一个元素作为pivot
4. 选择中位数作为pivot

2和3比较好写，比较方便，因为避免了两次交换过程。下面的代码选择以第一个元素作为pivot.

## 代码

```python
def quickSort(nums, left, right):
  if left >= right:
    return
  origin_l, origin_r = left, right
  pivot = nums[left]
	while left < right:
    while left < right and nums[left] <= pivot:
      left += 1
    while left < right and nums[right] >= pivot:
      right += 1
    nums[left], nums[right] = nums[right], nums[left]
  nums[left] = pivot
  quickSort(nums, origin_l, left)
  quickSort(nums, left+1, original_r)
    
```

