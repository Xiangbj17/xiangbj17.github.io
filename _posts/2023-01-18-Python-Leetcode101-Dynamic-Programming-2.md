---
title: Python Leetcode101 动态规划(下)
date: 2023-01-18 15:35:00 +0800
categories: [Leetcode, DynamicProgramming]
tags: [Algorithm]
render_with_liquid: false
---

动态规划的内容太多了，但确实其本身也很重要。

在动态规划（上）里面放了一维、二维、分割、子序列问题。

下面是背包、字符串编辑、股票交易问题。

# 背包问题

这个在Leetcode里面没有，但是是个经典的算法问题。在这里重新捋一捋数学上的东西。

## 题目

有 *N* 个物品和容量为 *W* 的背包，每个物品都有自己的体积 w 和价值 v，求：

拿哪些物品可以使得背包所装下物品的总价值最大。

- 如果限定每种物品只能选择 0 个或 1 个，则问题称为 0-1 背包问题;
- 如果不限定每种物品的数量，则问题称为无界背包问题或完全背包问题。

## 0-1背包问题的解法

可以看这个[视频](https://www.youtube.com/watch?v=nLmhmB6NzcM)，讲得很好，让我回忆起了大部分上课的时候的内容。

### 第一种解法：一个暴力的表格

```python
# 可以给出一个简单的例子，一共有四种物品，价值和质量分别如下：
p = [1, 2, 5, 6] 
w = [2, 3, 4, 5]
# 背包的总容量为
W = 8
```

那么，我们可以逐行地完成这样一个表格：

| `i`  | `pi & wi` | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| ---- | --------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | `(0, 0)`  | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| 1    | `(1, 2)`  | 0    | 1    | 1    | 1    | 1    | 1    | 1    | 1    |
| 2    | `(2, 3)`  | 0    | 1    | 2    | 3    | 3    | 3    | 3    | 3    |
| 3    | `(5, 4)`  | 0    | 1    | 2    | 5    | 5    | 6    | 7    | 7    |
| 4    | `(6, 5)`  | 0    | 1    | 2    | 5    | 6    | 6    | 7    | 8    |

其中，

- 每一行表示当我把第`i`个物品考虑进来的时候，对于不同的背包总容量，问题的解
- 每一列能表示当题目给出不同的`W`的时候，对于不同的物品，问题的解
- 第一行`(0, 0)`是边界条件：没有物品放的时候，就算背包容量是无穷大，价值也总为0
- 这个表的填写方式是从第一行到最后一行，先逐列填写完当前行，再填下一行

如果忘记了这个问题怎么去考虑，不妨重新再填一遍这个表，心里基本就有个思路了。

#### 状态转移方程及理解

根据这个填表的过程，可以想到状态转移方程：

- 首先，很明显有「第`i`个物品」和「背包总容量`j`」两个变量，**这是个二维动态规划问题**

- 假设`dp[i][j]`表示到第`i`个物品为止，背包总容量为`j`的情况下，能达到的最大价值

- 分别对两个变量进行阐述：

  - `i`其实是对问题进行子问题的拆分
    - 举个例子，假设一共有四个物品。当我开始考虑`i = 4`的情况就是：“我已经遍历完所有前三种物品的情况下，再加进来一个新的物品，接下来会怎样？”
  - `j`也是对问题进行子问题的拆分，只不过是在另一个维度
    - 比如，同样的4个物品，背包容量为8和背包容量为7这两个问题肯定是有联系的
    - 当我考虑`j = 8`的时候，就是：“我已经遍历完`j = 7`的所有情况了，如果背包容量再扩充一个，接下来会怎样？”

- 那么最重要的状态转移方程：

  - `dp[i][j] = max{dp[i-1][j], dp[i-1][j-w[i]] + p[i]}`

  - 比如，`dp[4][8] = max{dp[3][8], dp[3][8-5] + 6} = max{5, 2+6} = 8`

### 回溯解的过程

虽然得到了上面的表，但是那个物品选择放进去，哪个没有放，还是不明确。

需要从最后一行到第一行反向回溯。

我们对`res[4][8]`和它上面一个数`res[3][8]`进行比较，只要不等，说明这个当前这一行代表的物品选了

然后，继续找到`res[3][8-temp_weigt] = res[3][8-5]`，再比较它和它上面那个数等不等，一直找到`value=0`

![截屏2023-01-18 15 16 59](https://user-images.githubusercontent.com/84035000/213108876-a140e0f5-ee9f-4633-94ca-44158c1e0689.png)

#### Code

```python
def backpack01(w, p, W):
    res = [[0]*(W+1) for i in range(len(p)+1)]
    for i in range(1, len(res)):
        # 第0行是边界条件，从第一行开始逐行计算
        temp_w, temp_p = w[i-1], p[i-1]
        for j in range(len(res[i])):
            if j - temp_w >= 0:
                res[i][j] = max(res[i-1][j], res[i-1][j-temp_w]+temp_p)
            else:
                res[i][j] = res[i-1][j] 
    # 回溯解
    chosen = [0] * len(w) # 初始化选择列表
    i, j = len(res)-1, len(res[0])-1
    while res[i][j] != 0:
        if res[i][j] == res[i-1][j]:
            i -= 1
        else:
            chosen[i-1] = 1
            i, j = i-1, j-w[i-1]
    return res[-1][-1], chosen   # (8, [0, 1, 0, 1])
```

#### 在Jupyter Notebook上的效果

![img](https://user-images.githubusercontent.com/84035000/213101547-23b494a7-c213-4c73-aa2d-373c4f4d3dae.png)





# 416. [Partition Equal Subset Sum (Medium)](https://leetcode.cn/problems/partition-equal-subset-sum/)  

## 题目

给定一个非空数组`nums`，其中只有正整数，判断是否可以将其划分为两个子集，且两个子集的元素相等。

## Example

```python
输入：nums = [1,5,11,5]
输出：true
解释：数组可以分割成 [1, 5, 5] 和 [11] 。
```

## 思路

 和0/1背包问题的动态规划解法非常相似。

### 怎么去联系0/1背包问题

比较难想到的地方有两点，

- 一是要拽出来第二个维度
- 二是怎么拽出来这第二个维度

怎么能想到这两点：

- 首先，这个问题的解空间大小是`2^n`，因为每个数字我都可以选或者不选。根据这个，能联想到0/1背包问题，如果要一个一个遍历的话，也需要`2^n`次操作。想到0/1背包问题之后，就想0/1背包问题是怎么解决的
  - 它把物体的个数作为第一个维度，把背包的大小`W`作为第二个维度进行了二维动态规划
  - 那么，这个问题，是不是可以采用相似的解法。再拽出来第二个维度。
- 那，怎么拽？
  - 0/1背包问题的第二个维度是背包的大小，超出这个范围，我就不考虑了。
  - 在本问题中，我希望「选出来的数字之和」等于「所有数字总和」的一般，超出这个，我也不考虑了。
  - 这样，就可以设置「当前选出来的数字和」作为第二个维度。

### 列表找规律

就以上面Example中的`[1, 5, 11, 5]`为例，我可以列出来下面这个表，

其中，打钩的地方表示当前list中有的元素可以加和到这个数，其余地方不可以。

| 当前数字 | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   |
| -------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 無`(0)`  | √    |      |      |      |      |      |      |      |      |      |      |      |
| 1        | √    | √    |      |      |      |      |      |      |      |      |      |      |
| 5        | √    | √    |      |      |      | √    | √    |      |      |      |      |      |
| 11       | √    | √    |      |      |      | √    | √    |      |      |      |      | √    |
| 1        | √    | √    | √    |      |      | √    | √    | √    |      |      |      | √    |

规律：

- 如果`(i-1, j)`被打钩了，`(i, j)`一定也是一个钩钩
- 如果`(i-1, j-temp)`被打钩了，`(i, j)`也能是一个钩钩

根据这个规律，已经可以coding了，如果表的最后一行最后一列是钩钩，那就可以返回`True`。

## 题解

一次过的，太爽惹

```python
"""用时1456ms，超出72.50%用户；内存28.6MB，超出41.96%用户"""
class Solution(object):
    def canPartition(self, nums):
        total =  sum(nums)
        if total % 2 != 0:
            # 如果list加和为奇数，肯定没法对半拆
            return False
        # 创建一个存储解的二维表，共[len(nums)+1]行，(total/2+1)列
        res = [[0]*(total/2+1) for i in range(len(nums)+1)]
        res[0][0] = 1 # 边界条件，0个数加和为0，是可以实现的
        for i in range(1, len(res)):
            temp_num = nums[i-1]
            # print('i:', i, 'temp:',temp_num)
            for j in range(len(res[0])):
                if 0 <= j-temp_num <= len(res[0]):
                    res[i][j] = res[i-1][j] or res[i-1][j-temp_num]
                else:
                    res[i][j] = res[i-1][j]
            if res[i][-1] == 1:
                # 如果某一行的最后一列出现了True，已经可以返回True，提前终止了
                return True
        return False      
```



# 474. [ 一和零 (Medium)](https://leetcode.cn/problems/ones-and-zeroes/)

## 题目

- 有一个列表`strs`，其中存了一些由0和1组成的字符串
- 求`strs`**最多包含**`m`个0和`n`个1的最大的子集长度

## Example

```python
输入：strs = ["10", "0001", "111001", "1", "0"], m = 5, n = 3
输出：4
解释：最多有 5 个 0 和 3 个 1 的最大子集是 {"10","0001","1","0"} ，因此答案是 4 。
其他满足题意但较小的子集包括 {"0001","1"} 和 {"10","1","0"} 。{"111001"} 不满足题意，因为它含 4 个 1 ，大于 n 的值 3 。
```

## 思路

依旧是从一个list当中，选一些出来，满足一些条件，求xxx的最值的问题，解空间依旧是`2^n`

还是可以用0/1背包问题的老套路来做，但是这次二维表格要变成三维，因为有m和n两个条件。

比如说，当只考虑"10"的时候（1个0， 1个1）

| n\m  | 0    | 1    | 2    | 3    | 4    | 5    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| 1    | 0    | 1    | 1    | 1    | 1    | 1    |
| 2    | 0    | 1    | 1    | 1    | 1    | 1    |
| 3    | 0    | 1    | 1    | 1    | 1    | 1    |

继续考虑"0001"（3个0， 1个1）

| n\m  | 0    | 1    | 2    | 3    | 4    | 5    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| 1    | 0    | 1    | 1    | 1    | 1    | 1    |
| 2    | 0    | 1    | 1    | 1    | 2    | 2    |
| 3    | 0    | 1    | 1    | 1    | 2    | 2    |

再继续考虑"111001"（2个0, 4个1），表格没有变化

| n\m  | 0    | 1    | 2    | 3    | 4    | 5    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| 1    | 0    | 1    | 1    | 1    | 1    | 1    |
| 2    | 1    | 1    | 1    | 1    | 2    | 2    |
| 3    | 1    | 1    | 1    | 1    | 2    | 2    |

- 这里我们可以发现，一个max关系隐藏在里面
- 也就是说，如果这个二维表和上一个表比较起来，考虑进新str之后，数字至少不会比上一个表的数字小

再继续考虑"1"

| n\m  | 0    | 1    | 2    | 3    | 4    | 5    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| 1    | 1    | 1    | 1    | 1    | 1    | 1    |
| 2    | 2    | 2    | 2    | 2    | 3    | 3    |
| 3    | 2    | 2    | 2    | 2    | 3    | 3    |

……

到这里规律基本上就明显了

`dp[i][j][k] = max{dp[i-1][j-temp_0][k-temp1]+1, dp[i-1][j][k]}`

## 题解

```python
class Solution(object):
    def findMaxForm(self, strs, m, n):
        def count_0_1(str):
            zero, one = 0, 0
            for char in str:
                if char == '0':
                    zero += 1
                else:
                    one += 1
            return zero, one
        # 先开辟一个存储状态的空间[(len(strs)+1) x (m+1) x (n+1)] -> [6, 6, 4]
        res = [[[0]*(n+1) for i in range(m+1)] for j in range(len(strs)+1)]
        max_i, max_j, max_k = len(res), len(res[0]), len(res[0][0])
        #print(max_i, max_j, max_k)
        for i in range(1, max_i):
            temp_0, temp_1 = count_0_1(strs[i-1])
            #print('temp0', temp_0, 'temp1', temp_1)
            for j in range(max_j):
                # j 记录 0 的数量，如果0的数量满足要求，再继续往下看 1 
                if 0 <= j - temp_0 <= max_j:
                    for k in range(max_k):
                        # k 记录 1 的数量, 如果1的数量也满足要求，可以+1
                        if 0 <= k - temp_1 <= max_k:
                            res[i][j][k] = max(res[i-1][j][k] , res[i-1][j-temp_0][k-temp_1]+1)
                        else:
                            res[i][j][k] = res[i-1][j][k] # 直接抄表
                else:
                    # 0 的数量如果已经超了，看1也没意义了，直接抄[i-1]的表
                    for k in range(max_k):
                        res[i][j][k] = res[i-1][j][k]      
            #print(res[i])  
        return res[-1][-1][-1]
```



# 322. [coin change (Medium)](https://leetcode.cn/problems/coin-change/)

## 题目

- 一个整数数组`coins`，里面存了一些不同面额的硬币，每种面额的硬币有无限个（可以重复使用）
- 返回能凑成金额`amount`的最少的硬币个数。如果凑不成，返回-1.

## Example

```python
# Example 1
coins = [1, 2, 5]
amount = 11
res = 3 	# 11 = 5 + 5 + 1

# Example 2
coins = [1]
amount = 0
res = 0
```

## 分析

首先，这个如果还沿用0/1背包的二维方式，不太现实了。因为硬币无限，那么第一个维度将会是无穷。

那么，就只能砍掉第一个维度，保留第二个维度。

我们假设`dp[i]`表示：当前的硬币凑成面额`i`最少需要多少个硬币。用上面的例子，可以完成这个表：

| i     | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| dp[i] | 0    | 1    | 1    | 2    | 2    | 1    | 2    | 2    | 3    | 3    | 2    | 3    |

在填写上面这个表的过程中，可以发现规律：

`dp[i] = min{dp[i-k]+1} for k in coins`

边界条件就是`dp[0] = 0`，而且在初始化的时候，除了`dp[0]`之外的值都得初始化为-1

## 题解

```python
class Solution(object):
    def coinChange(self, coins, amount):
      	# 初始化dp
        dp = [0] + [-1] * amount
        for c in coins:
            if c <= amount:
                dp[c] = 1

        for i in range(1, len(dp)):
            if dp[i] != -1:
                continue # 说明前面initial的时候已经计算过
            temp_min = float("inf")
            for coin in coins:
              	# 注意这个地方: for coin， 不要 for j in range(i)，会超时
                if 0 <= i-coin <len(dp):
                    if dp[i-coin] != -1:
                        temp_min = min(temp_min, dp[i-coin]+1)
            if temp_min < float("inf") and temp_min != -1:
                dp[i] = temp_min
        return dp[-1]
```



# 字符串编辑

# 72. [Edit Distance (Hard)](https://leetcode.cn/problems/edit-distance/)

## 题目

给定两个单词`word1`和`word2`，返回将前者转换成后者需要的最少操作

操作一共有三种：1. 插入一个字符；2. 删除一个字符；3. 替换一个字符

## Example

```python
word1 = "horse"
word2 = "ros"
res = 3 
# horse -> rorse -> rose -> ros
```

## 思路

二维动态规划，`dp[i][j]`表示从`word1[i]`到`word2[j]`最少需要多少步，可以先把**边界条件**列出来，如下表所示：

| `dp` | j         | 0         | 1          | 2          | 3          |
| ---- | --------- | --------- | ---------- | ---------- | ---------- |
| i    |           | <u>''</u> | <u>r</u>   | <u>o</u>   | <u>s</u>   |
| 0    | <u>''</u> | 0         | 1          | 2          | 3          |
| 1    | <u>h</u>  | 1         | `dp[1][1]` | `dp[1][2]` | `dp[1][3]` |
| 2    | <u>o</u>  | 2         |            |            |            |
| 3    | <u>r</u>  | 3         |            |            |            |
| 4    | <u>s</u>  | 4         |            |            |            |
| 5    | <u>e</u>  | 5         |            |            |            |

剩下来的部分怎么填：

- `dp[1][1]`
  - 当我们考虑`i=1, j=1`的情况时，两个字符串新加进来的字符分别是`word1[1]=h`和`word2[1]=r`，不相等
  - 在考虑它和上，左，左上表格中的数的联系。
    - `dp[1][1]`和`dp[0][1]`分别是`h->r`和`""->r`
      - 我们可以先删掉`h`，将`word1`变成空字符串，然后就变成`dp[0][1]`的情况，那么有：
      - `dp[1][1] <= dp[0][1] + 1 = 1 + 1 = 2`
    - `dp[1][1]`和`dp[1][0]`分别是`h->r`和`h->""`
      - 相对于`h->""`的情况，我们可以添加一个字符`r`，实现` h -> "" -> r`，于是：
      - `dp[1][1] <= dp[1][0] +1 = 2`
    - `dp[1][1]`和`dp[0][0]`分别是`h->r`和`""->""`
      - 在后者的基础上，字符串尾部加入了两个不等的字符，只需要一次替换即可实现，那么：
      - `dp[1][1] <= dp[0][0] +1 = 1`
  - 所以`dp[1][1] = 1`

根据上面的思考方式，对于任意的`dp[i][j]`，我们先比较`word1[i]`是否等于`word2[j]`

- 相等
  - `dp[i][j] = dp[i-1][j-1] + 1`
- 不等
  - `dp[i][j] = min{dp[i-1][j-1], dp[i-1][j], dp[i][j-1]} + 1`

## 题解

```python
"""执行用时：104ms, 79.50%; 内存消耗：16.1MB, 94.53%"""
class Solution(object):
    def minDistance(self, word1, word2):
        # 初始化二维动态规划数组res,[[0,1,2,3,4,5],[1,1,1,1,1,1],[2,2,2,2,2,2],[3,3,3,3,3,3]]
        res = [[i for i in range(len(word2)+1)]] + [[j+1]*(len(word2)+1) for j in range(len(word1))]

        for i in range(1, len(res)):
            for j in range(1, len(res[0])):
                if word1[i-1] == word2[j-1]:
                    res[i][j] = res[i-1][j-1]
                else:
                    res[i][j] = min(res[i-1][j-1], res[i-1][j], res[i][j-1])+1

        return res[-1][-1]
```

# 650. [Two Keys Keyboard (Meduim)](https://leetcode.cn/problems/2-keys-keyboard/)

## 题目

- 只有一个copy和一个paste键
- 记事本上最初有一个`'A'`，求最少经过多少次操作，能恰好得到n个`'A'`

## Example

```python
# Example1
input = 3
res = 3  # copy 'A'， paste 两次，得到 'AAA'

# Example 2
input = 1
res = 0
```

## 思路

如果只用一个维度，没办法记录当前clipboard上面的数值，所以应该得用二维动态规划。

记`dp[i][j]`表示当需要得到`i`个A，剪贴板上有`j`个A的时候，最小的操作次数。

一个合法`[i][j]`对，必须满足`i >= j`，我们先列一个表分析一下`i<=8`的情况，这个表应该只有一半有数值

| `clipboard` \ `i` | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| ----------------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1                 | 1    | 2    | 3    | 4    |      |      |      |      |
| 2                 |      | 3    | -    | 4    |      |      |      |      |
| 3                 |      |      | 4    | -    |      |      |      |      |
| 4                 |      |      |      | 5    |      |      |      |      |
| 5                 |      |      |      |      |      |      |      |      |
| 6                 |      |      |      |      |      |      |      |      |
| 7                 |      |      |      |      |      |      |      |      |
| 8                 |      |      |      |      |      |      |      |      |

填表的时候，可以发现：

- 边界条件是`dp[1][1] = 1`
- 状态转移方程怎么考虑？从允许的操作去想。一次操作，要么进行粘贴，要么复制当前的数字。
  - 如果当前进行粘贴，剪贴版的数字不变，A的字符数量变化，那么：
    - 从这一分析可以得到`dp[i][j]`和前一行`dp[i-k][j]`的关系
    - 如果`j + k = 1`，从`(i-k, j)`状态出发，剪贴一次可以得到`i`，故有
    - <font color=green>`dp[i][j] = dp[i-k][j] if (j + k  == i)`</font>
  - 如果当前进行复制，那么剪贴板的数字变化，A的字符数量不变
    - 从这一分析可以得到`dp[i][j]`和`dp[i][i]`的关系为：
    - <font color=green>`dp[i][i] = min{dp[i][k]} + 1`</font>

- 如果最后要求的是`i`个A，就在`dp[i][*]`中取最小者作为解

## 题解

```python
class Solution(object):
    def minSteps(self, n):
        if n == 1:
            return 0
        res = [[-1]*n for i in range(n)] # 创建
        res[0][0] = 1
        for i in range(1, len(res)):
            # 搜相同的clipboard
            temp_min = float("inf")
            for j in range(i):
                if res[i-j-1][j] != -1:
                    res[i][j] = res[i-j-1][j] + 1
                    temp_min = min(res[i][j], temp_min)
            res[i][i] = temp_min + 1
        return temp_min
```

## 第二种方法（加入数学的考虑）

这个问题的解可以压缩到一维数组，如果把里面的数学逻辑捋清楚。

我们记`f[i]`表示有`i`个A的最小操作次数，稍微想一下，可以知道：

- `f[2i] = f[i] + 2`：如果已经有了`i`个A，再复制一次，粘贴一次，就能变成`2i`个A
- `f[x] = x`，对于质数来说，除了复制一次，粘贴`x-1`次，别无它法。

如果有一个合数`p`，它恰好可以分解成两个质数`p = m * n`，那么有：

- `f[p] = f[m] + f[n]`：首先我们操作`f[m]`次，拥有`m`个A，然后复制一次，粘贴`(n-1)`次，即可拥有`m * n`个A
- 而且因为`m, n`都是质数，有：`f[m]= m, f[n] = n`
- 故有`f[p] = m + n`

进一步，如果他可以分解成`p = p1^r1 * p2^r2 * p3^r3 ...`，其中`pi`均为质数，那么有：

- `f[p] = f[p1] * r1 + f[p2] * r2 + f[p3] * r3 + ... = p1*r1 + p2*r2 + p3*r3 + ...`

所以，<font color=green>对 n 做**质因数分解**，并将所有质因数乘以它的幂次并相加即可.</font>

那么，可以总结出来动态规划的规律。

不失一般性地，对于任意的整数`i > 1`，`f[i] = f[j] + f[i/j] (if i % j ==0)`

```python
class Solution(object):
    def minSteps(self, n):
        if n == 1:
            return 0
        temp = n
        res =  0                                                            
        for j in range(2, n+1):
            while temp % j == 0:
                res += j
                temp = temp / j
            if temp == 1:
                break
        return res
```

## 结果比较

<img src="https://user-images.githubusercontent.com/84035000/213613409-ed38b538-90fc-4f26-b61a-121b42b0bbc2.png" alt="image" style="zoom:50%;" />



# 10. [Regular Expression Matching (Hard)](https://leetcode.cn/problems/regular-expression-matching/)

## 题目

- 假设一种正则匹配规则：`.`匹配任意单个字符；`*`匹配零个或者多个前面的那一元素
- 给定一个字符串`s`和字符串**规律**`p`，返回能否将`s`匹配到`p`

## Example

```python
# Input: 
s = "aab", p = "c*a*b"
# Output:
res = True
```

## 思路

乍一看我感觉这个题不配Hard，但是仔细一想`. & *`的功能，其实还挺麻烦的，比如：

- `"a*"`可以表示`" ", "a", "aa", "aaa...."` 
- `"c*a*b"`可以表示`"b", "cb", 'ab', 'aaaab', 'cccaaab'`
- `".*"`表示的东西更多，能表示任何字符，或者没有字符

字符串匹配问题，通用方法还是得动态规划来，下面考虑怎么设计状态，状态转移方程，怎么找边界条件。

老规矩，还是先拿Example开刀。

题目中有两个`input`，一个`s`，一个`p`，比较符合二维动态规划，先用二维试试。

|            | `p[j]` | 0    | 1    | 2    | 3    | 4    | 5    |
| ---------- | ------ | ---- | ---- | ---- | ---- | ---- | ---- |
| **`s[i]`** |        | ""   | c    | *    | a    | *    | b    |
| 0          | ""     | √    | x    | √    | x    | √    | x    |
| 1          | a      | x    |      |      |      |      |      |
| 2          | a      | x    |      |      |      |      |      |
| 3          | b      | x    |      |      |      |      |      |

上面的表列出来边界条件`dp[0][0]`，以及按照个人感觉填出来的第一行第一列。

下面考虑如何去填充后一行。

- `dp[1][1], s = 'a', p = 'c'`
  - 新增的字符`s[1] = 'a'`，通配符是`p[1] = 'c'`
  - 两个新增的字符不一样，那么`dp[1][1] = False`
- `dp[1][2], s = 'a', p = 'c*'`
  - 新增的字符`s[1] = 'a'`，通配符是`p[2] = '*'`
  - 新增的字符还是不一样，`dp[1][2] = False`
- `dp[1][3], s = 'a', p = 'c*a'`
  - 新增的字符都是`a`，这时候可以追溯一下前面的情况
  - 发现`dp[0][2]`是`True`，那么`dp[1][3]`也是`True`
- `dp[1][4], s = 'a', p = 'c*a*'`
  - 新增的通配符是`p[4] = '*'`，这种情况如果上面一个是`True`，它应该也是`True`
  - 发现`dp[0][4]`是`True`，那么`dp[1][4]`也是`True`

根据上面的分析，填好第一行

|            | `p[j]` | 0    | 1    | 2    | 3    | 4    | 5    |
| ---------- | ------ | ---- | ---- | ---- | ---- | ---- | ---- |
| **`s[i]`** |        | ""   | c    | *    | a    | *    | b    |
| 0          | ""     | √    | x    | √    | x    | √    | x    |
| 1          | a      | x    | F    | F    | T    | T    | F    |
| 2          | a      | x    |      |      |      |      |      |
| 3          | b      | x    |      |      |      |      |      |

根据上面一行的填写过程，总结一下规律，对于任意的`(i, j)`：

- 如果`s[i] == p[j]`

  - `dp[i][j] == dp[i-1][j-1]`

- 如果`s[i] != p[j]`

  - 若`p[j] == '*'`，说明`p[j-1]`对应的字符可以出现多次，我们比较`s[i]`和`p[i-1]`的关系

    - 若`s[i] == p[j-1]`

      - `dp[i][j] = dp[i-1][j] or dp[i][j-2]`

      - 解释

        - `(i, j-2)`的情况，表示`x*`这个通配符匹配上了一次【牺牲两个`p`，换一个`s`】

          <img src="https://pic.leetcode-cn.com/1650902423-SwkmFq-LeetCode-10-3.png" alt="LeetCode-10-3.png" style="zoom:25%;" />

        - `(i-1, j)`的话，表示`x*`这个通配符匹配上了二次以上 

        <img src="https://pic.leetcode-cn.com/1650902717-XBZEcp-LeetCode-10-4.png" alt="LeetCode-10-4.png" style="zoom:25%;" />

    - 若`s[i] != p[j-1]`

      - `dp[i][j] = dp[i][j-2]`
      - 表示这个通配符一次都没匹配上

  - 若`p[j] == '.'`，我们认为`s[i] == p[j]`

    - `dp[i][j] == dp[i-1][j-1]`

## 题解

```python
class Solution(object):
    def isMatch(self, s, p):
        
        # 开辟保存状态的二维空间
        res = [[False]*(len(p)+1) for i in range(len(s)+1)]
        res[0][0] = True  # 边界条件

        # 第 0 行的条件自行讨论
        for j in range(1, len(res[0])):
            if p[j-1] == '*':
                res[0][j] = res[0][j-2]

        # 从第 1 行开始逐列逐行分析
        for i in range(1, len(res)):
            for j in range(1, len(res[0])):
                if p[j-1] == '.' or s[i-1] == p[j-1]:
                    res[i][j] = res[i-1][j-1]
                elif p[j-1] == '*':
                    if p[j-2] == s[i-1] or p[j-2] == '.':
                        res[i][j] = res[i-1][j] or res[i][j-2]
                    else:
                        res[i][j] = res[i][j-2]
                    
        return res[-1][-1]

```

## 执行结果

还不错，但是想递归方程着实恼火

<img src="https://user-images.githubusercontent.com/84035000/213655657-212ac959-4c42-492d-8279-672e970e5c80.png" alt="image" style="zoom:50%;" />

# 股票交易类问题

# 188. [Best Time to Buy and Sell Stock IV (Hard)](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/)

## 题目

- 有一个整数数组`prices`，储存了股票每一天的价格
- 限制最多进行`k`笔交易，求能够获取的最大利润

## Example

```python
k = 2, prices = [3, 2, 6, 5, 0, 3]
res = 7  # 第二天2元买入，第三天6元卖出(赚4元)；倒数第二天0元买入，最后一天3元卖出（赚3块钱）
```

## 思路

股票交易有贪心做法，但是为了避免贪心最后没办法推导到全局贪心，还是动态规划比较保底。

动态规划的话，得考虑这个问题的子问题有几个维度：

- `prices`数组的长度，也就是天数
- `k`有多大，也就是最多允许多少笔交易
- 光靠上面两个维度，难以推导状态转移方程，需要加入第三个维度，当日是否持股

定义`dp[i][j][k]`表示第`i`天，进行了`j`次交易，`k = 0`表示手中没有股票，`k = 1`表示手中有股票时**最大收益**。

状态转移方程：

- 对于任意的`(i, j, 0)`，手中没有股票，有两种情况：前一天有，今天卖掉；或者前一天也没有，今天继续不买
  - `dp[i][j][0] = max {dp[i-1][j-1][1] + prices[i], dp[i-1][j][0]`} 
- 对于任意的`(i, j, 1)`，手中有一股，也有两种情况：今天刚买一股；或者前一天买了一股，今天不动
  - `dp[i][j][1] = max{dp[i-1][j][0] - prices[i], dp[i-1][j][1]}`

边界条件（初始化）：

- 当`i = 0`时的所有情况，也就是第一天的时候：
  - `dp[i][*][0] = 0`：第一天不买，最大收益为0
  - `dp[i][*][1] = -prices[0]`：第一天持有股票，最大收益为负的第一天股价

- 当`k = 0`时的所有情况，也就是只有一次买卖的机会：
  - `dp[*][0][0] = 0`：永远也不买，永远都收益为0
  - `dp[i][0][1] = max{dp[i-1][0][0] - prices[i], dp[i-1][0][1]}`


## 题解

```python
class Solution(object):
    def maxProfit(self, k, prices):
        # 创建状态的存储空间res
        res = [[[0]*2 for _ in range(k+1)] for _ in range(len(prices))]

        # 初始化第一天的情况
        for j in range(k+1):
            res[0][j][0] = 0  # 手中没有持股，也就是没买，那就是0
            res[0][j][1] = -prices[0] # 手中持股，那就是负的第一天的价格

        # 初始化只允许买卖一次的情况
        for i in range(1, len(prices)):
            res[i][0][0] = 0  # 永远不持股，就是0咯
            res[i][0][1] = max(res[i-1][0][1], res[i-1][0][0] - prices[i])

        # 状态转移
        for i in range(1, len(prices)):
            for j in range(1, k+1):
                res[i][j][0] = max(res[i-1][j-1][1]+prices[i], res[i-1][j][0])
                res[i][j][1] = max(res[i-1][j][0]-prices[i], res[i-1][j][1])
                
        return max(res[-1][-1])
```



# 309. [Best Time to Buy and Sell Stock with Cooldown (Medium)](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

## 题目

- 整数数组`prices`包含了某些天的股票价格
- 约束：卖出股票后，无法在第二天买入（拥有一天的冷冻期）
- 求最大利润

## Example

```python
prices = [1, 2, 3, 0, 2]
res = 3 	# 1买入，2卖出，赚一块；0买入，2卖出，赚两块
```

## 思路

二维动态规划，用`dp[i][j]`表示

- 第一个维度记录天数
- 第二个维度有三个状态：持有股票，不持有股票且在非冷冻期，不持有股票且不处于冷冻期

初始化：

| 天数             | 0    | 1    | 2    | 3    | 4    |
| ---------------- | ---- | ---- | ---- | ---- | ---- |
| 0持有            | -1   |      |      |      |      |
| 1不持有&冷冻期   | 0    |      |      |      |      |
| 2不持有&不在冷冻 | 0    |      |      |      |      |

我们定义「**处于冷冻期**」为：

- 在卖出的当日为处于冷冻期的状态
- 冷冻期的第二天不能买，但是是通过查询前一天的状态发现自己不能买的

现在考虑第二天`dp[1][[*]`的情况：

- `dp[1][0]`持有股票，说明要么是第一天买了第二天按兵不动，要么是第一天没动第二天买，那么
  - `dp[1][0] = max{dp[0][0], dp[0][2]-prices[1]}` 
- `dp[1][1]`不持有，且处于冷冻期，那说明这一天卖掉了
  - `dp[1][1] = dp[0][0] + prices[1]`
- `dp[1][2]`不持有，且不处于冷冻期，可能前一天也不持有，今天也没买，这个状态和`(0, 1) & (0, 2)`都有关
  - `dp[1][2] = max{dp[0][1], dp[0][2]}`

根据这个其实就可以写一个状态转移方程了：

- `dp[i][0] = max{dp[i-1][0], dp[i-1][2]-prices[i]}`
- `dp[i][1] = dp[i-1][0] + prices[1]`
- `dp[i][2] = max{dp[i-1][1], dp[i-1][2]}`

## 题解

```python
class Solution(object):
    def maxProfit(self, prices):
        # 创建存储状态的空间res
        res = [[0]*3 for _ in range(len(prices))]

        # 初始化res[0][0], [0][1], [0][2]
        res[0] = [-prices[0], 0, 0]

        # 状态转移
        for i in range(1, len(res)):
            res[i][0] = max(res[i-1][0], res[i-1][2]-prices[i])
            res[i][1] = res[i-1][0] + prices[i]
            res[i][2] = max(res[i-1][1], res[i-1][2])

        return max(res[-1])
```

