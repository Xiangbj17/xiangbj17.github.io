---
title: Python Leetcode101:树相关 | Tree
date: 2023-03-12 16:30:00 +0800
categories: [Leetcode, Tree]
tags: [Algorithm]
render_with_liquid: false
---

# Part I 树的递归

# 104.Maximum Depth of Binary Tree (Easy)

## 题意

给定一个二叉树，求最大深度

## 题解

递归还是那么几个步骤，搞清楚函数的输入输出，对函数有一个明确的定义，就不会出错

可以一行解决

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        return max(self.maxDepth(root.left), self.maxDepth(root.right))+1 if root else 0
```

# 110. Balanced Binary Tree (Easy)

## 题意

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：

> 一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过 1 。

## 题解

这个题要注意：对于任何节点都要满足上述的定义，只要有一个不满足，就可以return False了

如果我们对于每个节点，都进行一样的操作，最好的选择就是递归

同时这个题，为了节省速度，可以定义一个「剪枝」策略：只要检索到不满足的节点，所有前面节点都不再计算了。

```python
class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        def dfs(node):
            # output: the height of the node if it's balanced, else -1
            if not node:
                return 0
            leftHeifht, rightHeight = dfs(node.left), dfs(node.right)
            if leftHeifht == -1 or rightHeight == -1:
                return -1
            if abs(leftHeifht - rightHeight) <= 1:
                return max(leftHeifht, rightHeight)+1
            else:
                return -1
        return dfs(root) != -1
```

# 543. Diameter of Binary Tree (Easy)

## 题意

一棵树的直径定义为：树的任意两个节点之间的无向距离。求给定树的最长直径。

## 题解

我一开始以为，返回`root`左子树和右子树的深度之和即可，后来想想，不是这么回事儿。

最大的距离不一定需要经过`root`，比如下图：`4 - 2 - 5 - 6 - 7`是最大的距离，没经过`root`

<img src="https://pic.leetcode-cn.com/bdb2395f9d30edcc515fabed62ab24a89b3d6c7c75237cc7fea27bb96852f5d4-%E5%B9%BB%E7%81%AF%E7%89%8710.JPG" alt="img" style="zoom:50%;" />

但是，最大距离还是值为`2`的节点的左子树和右子树深度之和。

这样的话，思路就是：对每一个节点，求其左子树和右子树深度之和，同时维护一个全局变量`res`，如果当前节点的最大距离大于`res`则更新`res`。

代码如下：

```python
class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 
        self.res = 0
        def getHeight(node):
            # return the height of temp node
            # the diameter will be calulated in this function meanwhile
            if not node:
                return 0 
            left = getHeight(node.left)
            right = getHeight(node.right)
            self.res = max(self.res, right + left)
            return max(left, right) + 1

        getHeight(root)
        return self.res         
```

# 437. Path Sum III (Medium)

## 题意

给定一个二叉树的根节点，以及一个整数`targetSum`，求二叉树中连续路径和等于这个整数的总路径数量

## Example

<img src="https://assets.leetcode.com/uploads/2021/04/09/pathsum3-1-tree.jpg" alt="img" style="zoom: 67%;" />

## 题解

只要提到「连续路径/子数组和」，请一定使用「前缀和」这个东西，这俩是绑定的。

### 为什么要用前缀和

以这棵树最左边的路径`[10, 5, 3, 3]`为例，如果我们不用前缀和，一个很naive的想法是列出「到当前node为止可以得到的和」，然后再加上当前的和。下面从`root`出发往下捋，说明这个想法的问题出在哪：

1. `root`: [0] -> [0, 10]
2. `val=5`: [0, 10] -> [0, 10, 5, 15]
3. `val=3`: [0, 10, 5, 15] -> [0, 10, 5, 15, 3, 13, 8, 18]

很显然，`13`是不应该出现的，因为`13 = 3(root.left.left) + 10(root)`，中间隔了一个节点，不是连续路径了。

而如果用前缀和的话，我们只需要检查，对于当前前缀和`preSum`，`preSum - targetSum`是否出现过，就能找到解了。且这个解一定是连续的节点的和。

题解如下

```python
class Solution:
    def pathSum(self, root: Optional[TreeNode], targetSum: int) -> int:
        self.res = 0
        def dfs(node, d, preSum):
            if not node:
                return
            tempSum = preSum + node.val
            self.res += d[tempSum-targetSum]
            new_d = d.copy()
            new_d[tempSum] += 1
            dfs(node.left, new_d, tempSum)
            dfs(node.right, new_d, tempSum)

        mem = defaultdict(int)
        mem[0] = 1
        dfs(root, mem, 0)
        return self.res
                
```

# 101. Symmetric Tree (Easy)

## 题意

判断一个二叉树是否对称。

## Example

![img](https://assets.leetcode.com/uploads/2021/02/19/symtree1.jpg)

## 题解

判断一个树是否对称，等价于判断左右子树是否对称，四步法：

1. 如果两个子树都为空指针，则它们相等或对称
2. 如果两个子树只有一个为空指针，则它们不相等或不对称
3. 如果两个子树根节点的值不相等， 则它们不相等或不对称
4. 根据相等或对称要求，进行递归处理。

```python
class Solution:
    def isSymmetric(self, root: Optional[TreeNode]) -> bool:
        def subSymmetric(node1, node2):
            # node1 and node2 are pairs that should be symmetric
            if not node1 and not node2:
                return True
            if not node1 or not node2:
                return False
            if node1.val != node2.val:
                return False
            return subSymmetric(node1.left, node2.right) and subSymmetric(node1.right, node2.left)
        
        return subSymmetric(root.left, root.right)
```



# 1110. Delete Nodes And Return Forest (Medium)

## 题意

给定一个整数二叉树和一些整数，求删掉这些整数对应的节点后，剩余的子树。

## Example

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/07/05/screen-shot-2019-07-01-at-53836-pm.png" alt="img" style="zoom:67%;" />

```python
root = [1,2,3,4,5,6,7], to_delete = [3,5]
res = [[1,2,null,4],[6],[7]]
```

## 思路

### 通过返回值来确定是否删除当前节点的子节点

递归一个比较麻烦的地方在于：对于`dfs(temp_node)`，如果我知道`temp_node`是要被删除的，我必须获得他的`partent`才能执行删除。那么是不是直接把父节点也送进来作为递归的参数呢？如果这样的话，另一个问题又来了，我怎么知道它是左子节点还是右子节点？如果要解决这个问题，又需要把左右送进来作为参数，太麻烦了。

更简单的方法是，通过递归return一个布尔值，如果`dfs(node.left)`返回`True`，那么就删掉这个左子节点。这样的话，递归的input和output就分别明确了，input是一个节点，output表示「这个节点是否是需要删除的」。

### res中的树根节点需要满足的条件

通过上面的讨论，我们解决了删除的问题。另一个待解决的问题是：删掉节点之后，会有一批新的根节点产生，如何正确识别这些树根节点，并append到res中去。

假设我们当前`dfs()`的节点是3，这个3是要被删除的。因此就会产生6和7，两个新的根节点。这个很好写，就`if temp_node.val in to_delete`，如果它的左右子节点存在，则加入到res中去。

真的是这样吗？假如这个example变一下，让7也在`to_delete`当中，这时候7就不能被加入res了。

所以真正的树根需要满足的有三个条件：(1) 其父节点在删除之列；(2) 它自己不在删除之列；(3) 这个点存在，不为None

题解如下：

```python
class Solution:
    def delNodes(self, root: Optional[TreeNode], to_delete: List[int]) -> List[TreeNode]:
         def dfsDelete(node):
            if not node:
                return False
            l, r = node.left, node.right # 因为后面可能会被删，所以先用指针取到这两个节点
            fl, fr = True, True					 # 如果子节点需要被删除，则置为 False
            if dfsDelete(node.left):
                node.left, fl = None, False
            if dfsDelete(node.right):
                node.right, fr = None, False
            if delete[node.val]:
                if l and fl: self.res.append(l)  # 满足三个条件，才会append
                if r and fr: self.res.append(r)
                return True
            return False

        if not root:
            return   
        self.res = []
        delete = Counter(to_delete)
        if not delete[root.val]:
            self.res.append(root) # 讨论特殊情况：root是否需要添加在res中
        dfsDelete(root)
        return self.res

```

其实仔细想，这属于后序遍历：先对子节点操作，完了再来管根节点

# Part II 树的层次遍历

# 637. Average of Levels in Binary Tree (Easy)

## 题意

给定一个二叉树，求每一层节点的平均值

## 题解

肯定是用广度优先搜索了，只有一个地方需要注意：需要用FIFO的队列来存节点的值，所以每次都是`pop(0)`

```python
class Solution:
    def averageOfLevels(self, root: Optional[TreeNode]) -> List[float]:
        tempList = [root]
        res = []
        while tempList:
            tempLen = len(tempList)
            count, total = 0, 0
            for _ in range(tempLen):
                node = tempList.pop(0)
                count, total = count+1, total+node.val
                if node.left: tempList.append(node.left)
                if node.right:tempList.append(node.right)
            res.append(total/count)
        return res
```

# Part III 前中后序遍历

# 105 Construct Binary Tree from Preorder and Inorder Traversal (Medium)

## 题意

给定一个二叉树的先序和中序遍历结果，还原这个二叉树

## Example

![img](https://assets.leetcode.com/uploads/2021/02/19/tree.jpg)

```python
preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
res = [3,9,20,null,null,15,7]
```

## 题解

preorder的第一个肯定是根节点，我们在inorder中找到这个节点，它左边的就是左子树的inorder，右边的就是右子树的inorder，除此之外，我们还可以获得左右子树的长度，从而可以在preorder中找到左右子树的preorder，即：

- `preorder = [root, [left tree's preorder], [right tree's preorder]]`
- `inorder = [[left tree's inorder], root, [right tree's inorder]]`

这样的话，其实都不用再写内部的递推函数，直接用`self.xxx()`不断传入新的preorder和inorder即可。

终止条件为，传入的list长度为0，返回None；长度为1，则返回一个Node

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder:
            return 
        if len(preorder) == 1:
            return TreeNode(val=preorder[0])
        tempNode = TreeNode(val=preorder[0])
        inorderIndex = inorder.index(preorder[0])
        tempNode.left = self.buildTree(preorder[1:1+inorderIndex], inorder[:inorderIndex])
        tempNode.right = self.buildTree(preorder[1+inorderIndex:], inorder[inorderIndex+1:])
        return tempNode
```

