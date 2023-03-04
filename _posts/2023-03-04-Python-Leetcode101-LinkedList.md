---
title: Python Leetcode101: 链表
date: 2023-03-04 12:07:00 +0800
categories: [Leetcode, LinkedList]
tags: [Algorithm]
render_with_liquid: false
---

链表(linked list)是一种由节点`Node`构成的数据结构，每个节点存有一个值`value`和一个指向下一节点的指针`next`。

一个简单的示意图如下：

<img src="https://user-images.githubusercontent.com/84035000/222875649-b573022c-0a79-4388-ba92-f1f259e87c51.png" alt="image"  />

通常可以用递归来处理链表问题。

这一篇里也重点总结了递归的写法和思路，见 206.Reverse Linked List (Easy) 部分。

# 1 构建链表 | Python

在第一部分，我们用Python构建一个链表类，以及定义一些常用的功能。



## 1.1 构建一个Node()类

链表的组成单位是`Node`，而它本身也是一种数据结构。因此我们先要定义`Node`类，才方便构建链表。

```python
class Node(object):
    def __init__(self, val=None):
        self.val = val
        self.next = None
```

`Node`由两部分组成，一个是`val`，在初始化的时候如果不传，自动赋值一个None，如果传了则赋予传入的值；第二部分是`next`，一般在初始化的时候先不给，在构建好下一个节点之后，再把前一个节点的指针指向它。



## 1.2 构建一个LinkedList()类并检测是否为空

有了`Node`，很容易构建一个`LinkedList`类了。

在最简单的情况下，它的成员变量只需要一个`self.head`，用来记录链表的头结点。对于这个头结点：

- 为了简便，在初始化的时候，我们可以先让它的`value = None`，后面再赋值。
- P.S. 如果一个链表的头结点值为`None`，我们说这个**链表是空的**，反之不为空。

在这里，我们同时实现一个`isEmpty`功能，来检测链表是否为空。

```python
class LinkedList(object):
    def __init__(self):
        self.head = Node()
        
    def isEmpty(self):
        return not self.head.val
      
""" 检验功能是否work """
>>> l = LinkedList()  # 建一个空的链表
>>> l.isEmpty()				# 查询链表是否为空
True
```



## 1.3 在尾部添加元素 | append()

在`append`的时候，我们需要考虑到链表此时是否是空状态：

- 如果为空，我们将待添加元素赋值给`head.val`
- 如果非空，我们遍历链表，找到最后一个节点（i.e. 最后一个没有`next`的节点），把新节点添加到它后面

```python
class LinkedList(object):
    # 为了节省一点版面，前面出现过的功能函数这里就省去了    
    def append(self, val):
        if self.isEmpty():
            self.head.val = val
        else:
            temp = self.head
            while temp.next:
                temp = temp.next
            temp.next = Node(val=val)
            
""" 检验功能是否work """
>>> l = LinkedList()
>>> l.append(1)
>>> l.isEmpty()
False
>>> l.head.val
1
```



## 1.4 打印链表所有的值 | travel()

在1.3里面，我们可以通过`l.head.val`来查看头结点的值，但如果要看第二个，第三个节点的值，就很麻烦了。

所以，需要实现一个函数`travel()`，方便查看链表已有的所有元素值。

```python
class LinkedList(object):
    def travel(self):
        head = self.head
        while head:
            print(head.val)
            head = head.next
        print('travel end')

""" 检验功能是否work """
>>> l = LinkedList()
>>> for i in [1, 2, 5]: l.append(i)
>>> l.travel()
1
2
5
travel end
```



## 1.5 在头部添加元素 | add()

头部添加元素的话，我们就需要为这个元素新建一个节点，把链表的head接在这个节点的后面。然后令这个节点为链表的新head。

```python
class LinkedList(object):
    def add(self, val):
        new = Node(val=val)
        new.next = self.head
        self.head = new
        
""" 检验功能是否work """
>>> l = LinkedList()
>>> for i in [1, 2, 5]: l.append(i)
>>> l.add(0)
>>> l.travel()
0
1
2
5
travel end
```



## 1.6 在链表中插入元素 | insert()

插入元素需要传入两个参数：`pos`表示在第几个位置插入，`val`表示插入元素的值。

这里需要对`pos`进行一个讨论：

- 如果`pos = 0`，那等价于在头部加入一个新元素，用之前定义过的`add()`方法即可。
- 如果`pos = len(LinkedList)`，即在末尾加入一个新元素，那我们把最后一个节点的指针指向新节点即可。
- 其余情况下，在`pos`位置插入元素，只需要把第`pos-1`个节点的指针指向新节点，再把新节点的指针指向原来位于的`pos`的元素即可。另外，在这个过程中，我们需要一个计数器`count`记录当前的节点深度。

上面的讨论都是基于`pos`是一个有效值说的，如果`pos`超出了valid范围，需要输出一个错误提示。

```python
class LinkedList(object):
    def insert(self, pos, val):
        if pos == 0:
            self.add(val)
        else:
            temp, count = self.head, 0
            while temp:
                count += 1
                if count == pos:
                    new = Node(val)
                    new.next = temp.next
                    temp.next = new
                    return
                temp = temp.next
            print('Insert Error -- position: [', pos, '] out of valid range!')
            
""" 检验功能是否work """
>>> l = LinkedList()
>>> for i in [1, 2, 5]: l.append(i)

# 在第1个位置insert一个0
>>> l.insert(1, 0)
>>> l.travel()
1
0
2
5
travel end

# 在第4个位置insert一个8
>>> l.insert(4, 8)
>>> l.travel()
1
0
2
5
8
travel end

# 在第111个位置insert一个2
>>> l.insert(111, 2)
Insert Error -- position: [ 111 ] out of valid range!

```



# 206. [Reverse Linked List (Easy)](https://leetcode.cn/problems/reverse-linked-list/)

## 题意

给定一个链表的头结点`head`，返回反转的链表

## Example

<img src="https://assets.leetcode.com/uploads/2021/02/19/rev1ex1.jpg" alt="img"  />

## 解法1: 递归

我自己写出来了一个递归，没有官方的简洁，而且是在函数内部定义了另一个函数，但是很好理解。下面还是讲一下官方的方法。

### (I) 先总结一下递归的一般方法

感觉递归要想写好，首先得给递归的函数给一个<font color=forestgreen>**非常明确的定义**</font>，包括：

- 这个递归函数的input是什么，output是什么
- 递归函数的作用是什么，在给出的example下，能代入每一步进行推导

第二步，是想好递归在哪里终止，终止条件是什么

第三步，我假设递归函数已经帮我处理好了某一部分，对于未被处理好的部分，我需要和被处理的部分做怎样的运算

就这三步，一步一步来就没错。

### (II) 以反转链表为例

第一步，我们定义一个函数`reverse(node)`，这个函数的input是一个节点，功能是：返回这个节点被反转了的链表，所以它的output是被反转的链表的头结点。

- 比如，对于上面Example的例子`[1 -> 2 -> 3 -> 4 -> 5]`，如果`reverse`传入的`node=3`，那么`reverse(node) = [5 -> 4 -> 3]`.

第二步，递归在原始链表的最后一个节点处终止，终止条件是`head.next == None`

第三步，对于每一个`node`，假设`reverse(node)`已经返回给我了被反转链表的头结点，那么我接下来怎么做？

- 这种情况下，想特例！特别是终止特例的前一次递归怎么做，根据这样的特例来写一般情况的代码。

- 假如，对`node = 4`的情况下，我用`reverse(node)`得到了`5`（并且这个`5`是终止条件），接下来，我肯定是要把`4`接到后面去。那我的做法就如下图所示：

  ![image](https://user-images.githubusercontent.com/84035000/222902469-ec54e791-599c-4f28-a5be-aa480e00eae8.png)

- 对`node=3`的情况，我们检验上述做法是否依旧可行，如下图所示：

  ![image](https://user-images.githubusercontent.com/84035000/222903068-168df95f-27c4-4636-913b-3cbd7c76cdc0.png)

- 发现依旧成立，得到一般结果。

### (III) 题解

总结一下这个过程，其实就是从最后一个节点往前捋，让每一个节点的下一个节点指向自己，再斩断和下一节点的联系。而递归的作用就是找到终止条件，以及实现从后往前的逆序顺序。

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if not head or not head.next:
          	# 这里 not head 是针对官网测试用例的，用例中有空链表
            return head
        
        new = self.reverseList(head.next)
        head.next.next = head
        head.next = None

        return new
```

## 解法2: 双指针迭代

这个解法可以通过下面的gif理解

<img src="https://pic.leetcode-cn.com/9ce26a709147ad9ce6152d604efc1cc19a33dc5d467ed2aae5bc68463fdd2888.gif" alt="img" style="zoom: 67%;" />

注意要用一个None来开局，每次迭代的时候，要记录下来第二个指针后面的值

我下面的代码和gif里面的指针名字是反过来的，因为我觉得排前面的才能叫pre，排后面的是cur，按照原顺序来的

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        pre, cur = None, head
        while cur:
            remain = cur.next
            cur.next = pre
            pre = cur
            cur = remain
        return pre
```

这个效率比递归快一些。



# 21. [Merge Two Sorted Lists (Easy)](https://leetcode.cn/problems/merge-two-sorted-lists/)

这题老朋友了

## 题意

有两个按「升等序」（我喜欢这么叫，不喜欢非降序的说法）排列的链表，把它俩合并一下，还是按升等序

## Example

<img src="https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg" alt="img" style="zoom:67%;" />

## 题解1: 迭代

大概就是，不动原来的两个链表，另外构造一个新的链表作为返回值。用两个指针分别指向原来的两个链表，谁小，当前的新链表赋谁的值，然后指针向后走一个。

这题有一个取巧的地方，最开始新建一个`head = Node(val=None, next=None)`作为头结点，后面两指针谁小谁接在它后面，最后返回`head.next`。这样的话就不用单独对`list1.head`和`list2.head`进行讨论了，可以一个while循环用到底。

需要注意的还有，while的终止条件是`p1 and p2`，也就是说，只要有一个链表走完了就终止循环。这时候可能某一个链表还有剩的，需要把剩的一并接在新的链表后面。不要忘了这个。

```python
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        res = temp = ListNode()
        p1, p2 = list1, list2
        while p1 and p2:
            if p1.val > p2.val:
                temp.next = ListNode(val=p2.val)
                temp, p2 = temp.next, p2.next
            else:
                temp.next = ListNode(val=p1.val)
                temp, p1 = temp.next, p1.next
        if p1:
            temp.next = p1
        else:
            temp.next = p2

        return res.next
```

## 解法2: 递归

还是老样子，首先明确递归的input和output，递归函数的功能。

- 这一题的题解格式就直接给出了一个建议，input是两个头结点，分别代表待整理的`list1`和`list2`。output只有一个，那就是已经sort好的新链表。递归函数`metgeTwoLists()`的功能就是返回输入两个链表合并后的新链表。

第二点，想递归的终止。

- 当传入的`list1`是空链表的时候，我们直接返回`list2`作为合并好的链表即可，反之亦然。
- 这里想递归终止的时候，可以代入一些Leetcode常用的空测试案例来想。

第三点，假设递归已经帮我们处理了一些东西，用特例总结一个一般情况。

- 首先，在一般情况下，`list1`和`list2`肯定都是非空的，不然就终止了。
- 那么，我们需要比较`list1.val`和`list2.val`谁小，然后把小的那个节点作为新链表的头（因为要返回一个新链表嘛，心里要时刻想着这个返回值怎么来才可以）
- 这时候，我们把剩下没比较的东西交给`mergeTwoLists()`即可。

代码如下：

```python
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        if not list1:
            return list2
        if not list2:
            return list1
        if list1.val < list2.val:
            list1.next = self.mergeTwoLists(list1.next, list2)
            return list1
        else:
            list2.next = self.mergeTwoLists(list1, list2.next)
            return list2
```

