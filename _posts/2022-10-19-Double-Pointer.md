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



# 88. [Merge Sorted Array (Easy)](https://leetcode.cn/problems/merge-sorted-array/)

## 题目大意

- `nums1`和`nums2`是两个有序数组，长度分别是`m+n`和`n`
- 其中`nums1`在前*m*项有数，后*n*项为0
- 需要把数组`nums2`归并到`nums1`去，并且保持有序

## 示例

```python
nums1 = [1, 2, 3, 0, 0, 0], m = 3
nums2 = [2, 5, 6], n = 3
res = [1, 2, 2, 3, 5, 6]
```

## 思路

感觉Leetcode题解里面的一个图画的比较清晰

![img](https://pic.leetcode-cn.com/42ef79e54740c9788b4563d82f7bbd025fee4271c72fc11e8716a8b9c1b2e747-%E5%9B%BE%E7%89%87.png)

三个指针：

- P_end（黄） 从`nums1[m+n]`处开始，左移
- P_1（蓝） 从`nums1[m]`开始，左移
- P_2（绿） 从`nums2[n]`开始，左移

<img src="https://pic.leetcode-cn.com/66fbd7447f00675f44a40c9c99677cc2549c323f7c6a5385291edb1373e5c7dc-%E5%9B%BE%E7%89%87.png">

规则：

当p1指针和p2指针都为正的时候：

- 如果p1指针的值大于p2指针，那P_end的值为p1，反之为p2
- 替换元素的指针往左移动一位，p_end也往左移一位

上一个循环走完之后，要么p1走到头，要么p2走到头。

- 如果p1走到头，p2有可能没走完，所以需要继续把p2的值复制过来
- 如果p2走到头，可以直接return了

## 题解

```python
class Solution(object):
    def merge(self, nums1, m, nums2, n):
        p_end = m + n -1
        p1, p2 = m - 1, n - 1
        while p1 >= 0 and p2 >= 0:
            if nums1[p1] > nums2[p2]:
                nums1[p_end] = nums1[p1]
                p1 -= 1
                p_end -= 1
            else:
                nums1[p_end] = nums2[p2]
                p2 -= 1
                p_end -= 1
        while p2 >= 0:
            nums1[p_end] = nums2[p2]        
            p2 -= 1
            p_end -= 1
        return 
```

# 142. [ Linked List Cycle II(Medium)](https://leetcode.cn/problems/linked-list-cycle-ii/)

## 题目

​	给定一个链表，如果链表有环，返回环开始的地方，若没有返回None

## 示例

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

```python
head = [3, 2, 0, -4]
return Node # 环从第一个节点开始
```

## 思路

[Floyd's tortoise and hare](https://en.wikipedia.org/wiki/Cycle_detection)

![img](https://web.ntnu.edu.tw/~algo/CycleFinding4.png)

设两指针 `fast`，`slow` 指向链表头部 `head`，`fast` 每轮走 2步，`slow` 每轮走 1 步

**Case 1：** `fast` 指针走过链表末端，说明链表无环

**Case 2：** 当`fast == slow`时， 两指针在环中**第一次相遇** 。此时`fast` 与 `slow`走过的 **步数关系** 如下：

- 设链表共有 a+b 个节点，其中链表头部到链表入口有 a个节点（不计链表入口节点）， 链表环 有 b 个节点，设两指针分别走了 f，s 步，则有：

$$
f=2s\;, f =s+nb \to \; f=2nb\;,\; s=nb
$$

- 此时，`slow` 指针**位置不变** ，将`fast`指针重新指向链表头部节点；`slow`和`fast`同时每轮向前走 1步；此时 *f* =0，s = nb
- 当 `fast` 指针走到*f* = *a* 步时，`slow` 指针走到 *s*=*a*+*nb*，此时**两指针重合，并同时指向链表环入口** 。

## 题解

```python
class Solution(object):
    def detectCycle(self, head):
        slow, fast = head, head
        while True:
            if not (fast and fast.next): return
            slow, fast = slow.next, fast.next.next
            if slow == fast: break
        fast = head
        while fast != slow:
            slow, fast = slow.next, fast.next
        return fast
```

# 76. [Minimum Window Substring (Hard)](https://leetcode.cn/problems/minimum-window-substring/)

## 题目大意

- 给定一个字符串 `s` 、一个字符串 `t` 
- 返回 `s` 中涵盖 `t` 所有字符的最小子串，没有则返回空字符串 `""` 

## 示例

```python
s = "ADOBECODEBANC", t = "ABC"
res = "BANC"
```

## 思路

【滑动窗口】

用i,j表示滑动窗口的左边界和右边界，形成一个窗口在字符串上游走，步骤为：

- 首先不断增加`j`使滑动窗口增大，直到窗口包含了所需所有元素
- 不断增加`i`使滑动窗口缩小，去除不必要的元素，使长度减小，直到碰到一个必须包含的元素，这个时候不能再扔了，再扔就不满足条件了，记录此时滑动窗口的长度，并保存满足要求的最小子串
- 让`i`再增加一个位置，这个时候滑动窗口肯定不满足条件了，那么继续从**步骤一**开始执行，寻找新的满足条件的滑动窗口，如此反复，直到`j`超出了字符串S范围。

【哈希表】

为了快速判断当前窗口内是否包含需要的字符，维护一个字典来记录还缺哪些：

在上面的例子中`hash={'A':1, 'B':1, 'C':1}`，当找到一个A，A对应的value减1即可，如果扔掉一个A，对应value加1.

为了避免每次都遍历哈希表，可以再维护一个int变量，这个变量等于hash所有的value之和，也就是当前“还缺多少字母”

## 题解

```python

```











