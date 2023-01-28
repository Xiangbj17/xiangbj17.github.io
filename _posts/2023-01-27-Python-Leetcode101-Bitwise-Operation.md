---
title: Python Leetcode101 位运算
date: 2023-01-27 19:15:00 +0800
categories: [Leetcode, Bitwise Operation]
tags: [Algorithm]
render_with_liquid: false
---

# 位运算的技巧

## 1. 按位异或<^>

- `x ^ 0s = x`：任何数和一串0异或，得到本身
- `x ^ 1s = ~x`：任何数和一串1异或，得到自己的反
- `x ^ x = 0`：任何数和自己异或，得到0

## 2. 按位与<&>

- `x & 0s = 0`：任何数和一串0与，得到0
- `x & 1s = x`：任何数和一串1与，得到本身
- `x & x = x`：任何数和自己与，得到本身

## 3. 按位或<|>

- `x | 0s = x`：任何数和一串0或，得到本身
- `x | 1s = 1s`：任何数和一串1或，得到一串1
- `x | x = x`：任何数和自己或，得到自己

## 4. 一些技巧

- 清除二进制最低位的1：`x & (x-1)`
  - 如果一个数`n`是二的次方，一定有 ` n & (n-1) = 0`
- 得到二进制最低位的1：`x & -x`
- 两数取平均：`mid = (left + right) // 2 `等价于`mid = (left + right) >> 1`
- 二进制减去一位：`x = x >> 1`
- 生成26位字母掩码：`for c in word: bitmask |= 1 << (ord(c) - ord('a'))`

# 位运算基础问题

# 461. [Hamming Distance (Easy)](https://leetcode.cn/problems/hamming-distance/)

## 题目

定义：两个整数之间的汉明距离是指两个数字对应二进制位不同的数量

给定两个整数`x, y`，计算并返回他们之间的汉明距离 

## Example

```python
x, y = 1, 4
res = 2
""" Explain
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑
上面的箭头指出了对应二进制位不同的位置
"""
```

## 思路

首先来一个按位异或`x ^ y`，比如上面的例子可以得到`0 1 0 1`

然后统计得到的数字里面1的数量，就可以得到汉明距离

## 代码

```python
class Solution(object):
  """执行用时：12 ms, 在所有 Python 提交中击败了97.76%的用户
		 内存消耗：12.7 MB, 在所有 Python 提交中击败了99.04%的用户"""
    def hammingDistance(self, x, y):
        xor = x ^ y
        res = 0
        while xor:
            res += xor & 1  # 统计最后一位是不是1
            xor = xor >> 1  # xor往左减一位
        return res
```

```python
# 一行代码版本
class Solution(object):
  """执行用时：16 ms, 在所有 Python 提交中击败了81.73%的用户
		 内存消耗：12.8 MB, 在所有 Python 提交中击败了88.46%的用户"""
    def hammingDistance(self, x, y):
        return bin(x ^ y).count("1")
```

# 190. [Reverse Bits (Easy)](https://leetcode.cn/problems/reverse-bits/)

## 题目

颠倒给定的 32 位无符号整数的二进制位

## Example

```python
n = 00000010100101000001111010011100
res = 964176192 (00111001011110000010100101000000)
# 解释：输入的二进制串 00000010100101000001111010011100 表示无符号整数 43261596，
#     因此返回 964176192，其二进制表示形式为 00111001011110000010100101000000。
```

## 题解

```python
class Solution:
    def reverseBits(self, n):
        res = 0
        for _ in range(32):
            res <<= 1
            res += n & 1
            n >>= 1
        return res
```



# 136. [Single Number (Easy)](https://leetcode.cn/problems/single-number/)

## 题目

一个非空数组`nums`里，除了某个元素只出现了一次之外，其余元素均出现了两次。

找出只出现了一次的元素

## Example

```python
nums = [4, 1, 2, 1, 2]
res = 4
```

## 思路

由于`x ^ x = 0`且`x ^ 0s = x`，用所有元素与0进行异或，最后得到的值就是只出现了一次的元素

## 题解

```python
class Solution(object):
    def singleNumber(self, nums):
        res = 0
        for num in nums:
            res = res ^ num
        return res
```

# 二进制特性

# 342. [Power of 4 (Easy)](https://leetcode.cn/problems/power-of-four/)

## 题目

给定一个整数`n`，判断他是否是4的幂次方

## 传统解法

```python
 class Solution(object):
    def isPowerOfFour(self, n):
        """
				执行用时：32 ms, 在所有 Python 提交中击败了16.15%的用户
				内存消耗：13.1 MB, 在所有 Python 提交中击败了26.92%的用户
        """
        if n == 0: return False
        while n != 1:
            res = n % 4
            if res == 0:
                n //= 4
            else:
                return False
        return True
```

## 思路

- 如果一个数是4的次方，那么他首先也是2的次方。那么在二进制中，它一定只有一个1，其余位置都是0
  - 如果`n & (n-1) = 0`，那么这个数是2的次方
- 如果一个数还是4的次方，那么在二进制中，1的位置一定在奇数位。
  - 如果` n & (10101...101) != 0`，那么这个数，在2次方的基础上，也是4的次方。
  - 这个 `(10101...101)`用十六进制表示为`(AAAAAAAA)`

## 题解

```python
class Solution(object):
    def isPowerOfFour(self, n):
		"""
		执行用时：24 ms, 在所有 Python 提交中击败了66.92%的用户
		内存消耗：13 MB, 在所有 Python 提交中击败了42.31%的用户
		"""
        return n > 0 and (n & (n-1)) == 0 and (n & 0xaaaaaaaa) ==0
       
```



# 318. [Maximum Product of Word Lengths (Medium)](https://leetcode.cn/problems/maximum-product-of-word-lengths/)

## 题目

给定一个字符串数组`words`，找出并返回不含有公共字母的两个单词`len(words[i]) * len(words[j])`的最大值

如果不存在这样的两个单词，返回0

## Example

```python
words = ["abcw","baz","foo","bar","xtfn","abcdef"]
res = 16 
# 解释：这两个单词为 "abcw", "xtfn"。
```

## 思路

- 首先将每个单词用可以一个26位掩码表示
  - 比如`"abce"`用`(0000...10111)`表示
  - 那么，有相同字母的单词掩码就有`x & y != 0`
    - 因为必须两个都是`1`，与`"&"`才是`1`
  - 没有相同的字母，就有`x & y = 0`
    - 如果是`(0, 1), (0, 0)`，与永远是零`0`，所以结果也是0
- 然后，建立一个字典记录每一个掩码对应的最长单词
- 两两比较遍历字典里的所有掩码，获得题解



## 题解

```python
class Solution(object):
    def maxProduct(self, words):
      """
      执行用时：180 ms, 在所有 Python 提交中击败了93.94%的用户
			内存消耗：17.6 MB, 在所有 Python 提交中击败了75.76%的用户
      """
        mask_dict = {}
        for word in words:
            bitmask = 0    # 初始化26位掩码
            for c in word:
                bitmask |= 1 << (ord(c) - ord('a')) # 1进n位再与Bitmask与一下
            if bitmask in mask_dict:
                mask_dict[bitmask] = max(len(word), mask_dict[bitmask])
            else:
                mask_dict[bitmask] = len(word)

        res = 0
        for i in mask_dict.keys():
            for j in mask_dict.keys():
                if i & j == 0:
                    res = max(res, mask_dict[i] * mask_dict[j])
        
        return res
```

# [338. Counting Bits (Easy)](https://leetcode.cn/problems/counting-bits/)

## 题目

`n`是一个非负整数，以list的形式返回从0到n的所有数字二进制表达中的`"1"`的个数

## Example

```python
n = 5
res = [0, 1, 1, 2, 1, 2]
```

## 思路

说实话一看到这个题目我就想到动态规划（实际上也是动态规划），那重要的就是状态转移方程

定义：`dp[i]`表示数字`i`二进制表示中含有的1的数量

规律：

- 若`i`二进制最后一位为1，那么`dp[i] = dp[i-1] + 1`
- 若`i`二进制最后一位为0，说明进了位，`dp[i] = dp[i>>1]`

边界条件：

- `dp[0] = 0`

## 题解

```python
class Solution(object):
        res = [0] * (n+1)
        for i in range(1, len(res)):
            if i & 1 == 1: 
                # 最后一位为1
                res[i] = res[i-1] + 1
            else:
                res[i] = res[i >> 1]
        return res
```

