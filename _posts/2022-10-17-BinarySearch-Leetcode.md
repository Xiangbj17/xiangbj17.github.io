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

# 81. [Search in Rotated Sorted Array II (Medium)](https://leetcode.cn/problems/search-in-rotated-sorted-array-ii/)

## 题意

`nums`原本是一个非降序排列整数数组，可是为了增加题目难度，把它在未知的某个下标 `k`（`0 <= k < nums.length`）上进行了**旋转**，使之变为
$$
[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]
$$
对于旋转后的数组`nums`，看看`target`是否存在，存在则返回True，否则返回False

Attention：数组中可能会存在重复的元素

## 示例

```python
nums = [2,5,6,0,0,1,2], target = 0
res = True
```

## 思路

还是用二分查找的模板，主要有两个点：

1. left-mid之间和mid-right之间肯定有一边是有序的，需要先判断哪边有序，然后利用这一边做排除法。
   - 有没有序很好判断，如果`nums[left]<nums[mid]`，那就是左边有序，如果大于，右边有序
   - 接下来，通过左右极小极大值判断target是不是在有序的这一段里
     - 在就继续搜索这里面
     - 不在就继续搜索另一半
2. `nums[left]=nums[mid]`的情况是有可能出现的，此时无法判断哪边有序
   - 这时候，我们首当其冲地判断一下`nums[mid] == target`
     - True就谢天谢地直接返回
     - False的话，既然left和mid相等，那left和target也不等，直接left+1即可，往后走一个。

## 题解

```python
class Solution(object):
    def search(self, nums, target):
        if len(nums) == 1: return nums[0] == target
        left, right = 0, len(nums)-1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target: return True
            if nums[mid] == nums[left]: 
                left += 1
            elif nums[mid] > nums[left]:
                # Left half in order
                if nums[left] <= target < nums[mid]: right = mid - 1
                else: left = mid + 1
            else:
                # Right half in order
                if nums[mid] < target <= nums[right]: left = mid + 1
                else: right = mid - 1
        return False
```



# 154. [Find Minimum in Rotated Sorted Array II (Hard)](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/)

## 题意

`nums`原本是一个非降序排列整数数组，可是为了增加题目难度，把它在未知的某个下标 `k`（`0 <= k < nums.length`）上进行了**旋转**，使之变为
$$
[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]
$$
对于旋转后的数组`nums`，寻找其最小值

Attention：数组中可能会存在重复的元素

## 示例

```python
nums = [1,3,5]
return 1
```

## 思路

和上一题一样，连注意事项都一模一样。

因为可能会出现重复的元素所以需要处理mid和right或者mid和left相等的情况。

## 题解

```python
class Solution(object):
    def findMin(self, nums):
        if len(nums) == 1: return nums[0]
        res = float('inf')
        left, right = 0, len(nums)-1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] < res:
                res = nums[mid]
            if nums[mid] == nums[right]:
                # Hald to distinguish
                right -= 1
                continue
            elif nums[mid] < nums[right]:
                # Right half in order
                right = mid - 1 # search in left
            else:
                # Left Half in order, search right
                if nums[left] < res:
                    res = nums[left]
                left = mid + 1 # search in right
        return res
```

# 540. [Single Element in a Sorted Array (Midium)](https://leetcode.cn/problems/single-element-in-a-sorted-array/)

## 题意

- `nums`: 整数数组，非降序排列，其中仅有一个元素只出现一次，其余元素均出现两次
- 返回只出现了一次的元素
- 解决方案必须满足 `O(log n)` 时间复杂度和 `O(1)` 空间复杂度。

$$
2^x=n, x = log_2n
$$



## 示例

```python
nums = [1,1,2,3,3,4,4,8,8]
res = 2
```

## 思路

观察示例可以发现

```
nums = [1,1,2,3,3,4,4,8,8]
odev = [e,o,e,o,e,o,e,o,e]
```

- 对于出现在target左边的元素：
  - 如果这个数所在的序号`i`为奇数，那么与之相等的数一定在它的左边
  - 偶数，则与之相等的数一定在其右边
- 对于出现在target右边的元素：
  - 如果`i`为奇数，那么与之相等的数一定在它的右边
  - 偶数，则在左边

通过上述规律来判断target究竟在左半边还是在右半边，即：

- 如果mid是偶数，与之相等的数在其左边，那么target在左半边，反之在右半边
- 如果mid是奇数，与之相等的数在其左边，那么target在右半边，反之在左半边

### 这道题的技巧：

利用按位异或的性质，可以得到 mid 和相邻的数之间的如下关系，其中 ^ 是按位异或运算符：

- 如果mid是偶数，mid ^ 1 = mid + 1
- 如果mid是奇数，mid ^ 1 = mid - 1

也就是说，如果 `nums[mid ^ 1] == nums[mid]`，说明：

- 如果mid是偶数，那么判断mid是否与mid+1相等
- 如果mid是奇数，那么判断mid是否与mid-1相等

True则都能说明target在右边，根据这个写代码

### 和模板不一样的地方:

#### 第一处

如果用了`nums[mid ^ 1] == nums[mid]`这种判断方式，在相等的情况下，能说明mid是有匹配的；

但如果不相等的话，有两种情况

1. mid有匹配，但在 `mid ^ 1` 的反方向一边
2. mid没有匹配，就是target

鉴于无法排除情况2，所以在不相等的情况下，不能贸然做`right=mid-1`的操作，只能`right=mid`

#### 第二处

循环的条件需要变成`left < right`，因为如果`left=right`，没有计算下去的必要

### 第三处

循环返回left或者right都行，可以通过示例来推，最后一次循环，三个指针分别在0,1,2处

并且`nums[mid^1] = nums[mid]`，根据算法，`left`跑到2去，也就是left=right=2

但mid还在1，所以我们返回left和right都行

## 题解

```python
class Solution(object):
    def singleNonDuplicate(self, nums):
        if len(nums) == 1: return nums[0]
        left, right = 0, len(nums)-1
        while left < right:
            mid = (left + right) // 2
            if nums[mid^1] == nums[mid]:
                left = mid + 1
            else:
                right = mid
        return nums[left]
```

