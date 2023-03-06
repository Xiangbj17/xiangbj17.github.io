---
title: Python Leetcode101:链表 Linked List 2 | 技巧
date: 2023-03-06 16:38:00 +0800
categories: [Leetcode, LinkedList]
tags: [Algorithm]
render_with_liquid: false
---

# 160. [Intersection of Two Linked Lists (Easy)](https://leetcode.cn/problems/intersection-of-two-linked-lists/)

## 题目

给定两个链表头结点，试着找他们有无相交节点，没有则返回`None`

## Example

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png" alt="img" style="zoom:67%;" />

## 题解: 拼接链表

对于上面的例子，用两个指针`pa, pb`分别从`headA`和`headB`出发，以相同的速度向后移动。

当某一个指针走到尽头的时候，就重新从另一个链表的头开始走。

<img src="https://user-images.githubusercontent.com/84035000/223061203-93d5a0b3-cd93-4594-aea9-48d4e83e07ee.png" alt="image" style="zoom:67%;" />

这样，两个指针就都走了相同的步数，且最终会在`c1`的地方相遇。

如果两个链表没有相交点，这个两个指针在`None`处相遇。

所以，只要两个指针相等，任意返回其中一个指针即可。

```python
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> Optional[ListNode]:
        pa, pb = headA, headB
        while pa != pb:
            pa = pa.next if pa else headB
            pb = pb.next if pb else headA
        return pa
```



# 142. [Linked List Cycle II (Medium)](https://leetcode.cn/problems/linked-list-cycle-ii/)

## 题意

给另一个链表的头结点`head`，如果链表有环，返回入环的第一个结点，没有则返回`None`

## Example

<img src="https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png" alt="img" style="zoom: 67%;" />

返回`val = 2`的那个结点

## 解法

这个可以哈希表解决，但时空复杂度都是`O(n)`。这个题，取巧的话，可以用[Floyed的龟兔赛跑算法(**Tortoise and Hare Algorithm**)](https://zh.wikipedia.org/wiki/Floyd%E5%88%A4%E5%9C%88%E7%AE%97%E6%B3%95)解决，空间复杂度能缩减到`O(1)`，也就是只用两个指针。

方法如下，两个指针`hare`和`tortoise`都同时从`head`出发，兔子每次走两步，乌龟每次走一步。如果兔子走着走着抵达了`None`，说明是没有环的，就可以直接return。

如果在这个过程中，龟兔相遇了，就说明是有环的。

相遇之后，让他们中的一个从头结点重新出发，且两个动物步长都改为1。他们再次相遇的节点，即为我们需要返回的节点。

下面给出一个简单直观的数学推导

<img src="https://user-images.githubusercontent.com/84035000/223125874-6c7d874d-11cd-4866-a806-6145950f930c.png" alt="image" style="zoom:67%;" />

代码：

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        hare, tortoise = head, head
        while True:
            if not hare: return
            if not hare.next: return 
            hare = hare.next.next
            tortoise = tortoise.next
            if hare == tortoise:
                break
    
        hare = head
        while hare != tortoise:
            hare = hare.next
            tortoise = tortoise.next
        return hare
```

