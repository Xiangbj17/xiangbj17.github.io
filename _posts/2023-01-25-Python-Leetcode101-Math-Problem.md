---
title: Python Leetcode101 数学问题巧解
date: 2023-01-25 14:20:00 +0800
categories: [Leetcode, Math]
tags: [Algorithm]
render_with_liquid: false
---

# 1 公因数和公倍数

## 求两个数的最大公因数

`Greatest Common Divisor, GCD`可以使用辗转相除法

```python
def gcd(a, b):
    if b == 0:
        return a
    else:
        return gcd(b, a % b) # 调用自己继续除
        
>>> gcd(12, 16)
>>> 4
```

## 求两个数的最小公倍数

`Least Common Multiple, LCM`可以将两个数相乘再除以GCD

```python
def lcm(a, b):
    return a * b / gcd(a, b)
 
>>> lcm(12, 16)
>>> 48.0
```

# 2 质数

质数（或者素数），是指在大于 1 的自然数中，除了 1 和它本身以外不再有其他因数的数。

值得注意的是，每一个数都可以分解成质数的乘积。

# 204. [Count Primes (Meduim)](https://leetcode.cn/problems/count-primes/)

## 题目

给定一个非负整数`n`，返回所有小于`n`的质数的数量

## Example

```python
# input
n = 10
# output
res = 4 # [2, 3, 5, 7]
```

## 思路

#### 埃拉托斯特尼筛法(Sieve of Eratosthenes，简称埃氏筛法)

原理：从 1 到 *n* 遍历，假设当前遍历到 *m*，

- 把所有小于 *n* 的、且是 *m* 的倍数的整数标为和数;
- 遍历完成后，没有被标为和数的数字即为质数。

```python
class Solution(object):
    def countPrimes(self, n):
        if n < 3:
            return 0     
        output = [1] * n
        # 因为0和1不是质数, 前两个位置赋0
        output[0], output[1] = 0, 0
        for i in range(2,int(n**0.5)+1): 
          	# 是合数就不算了，是质数再算，这样是避免4、8...把2算过的再算一遍
            if output[i] == 1:
                # [i*i:n:i]是指从i*i开始，到n，每隔i个改变一次
                # i*i是因为在(i-1)的时候，(i-1)*i已经被算过了，所以从i*i开始
                output[i*i:n:i] = [0] * len(output[i*i:n:i])
        # 最后output中的数字1表明该位置上的索引数为质数,然后求和即可.
        return sum(output)
```



# 数字处理

# 504. [Base 7 (Easy)](https://leetcode.cn/problems/base-7/)

## 题目

给定一个整数`num`，将其转化为7进制，并输出成字符串

## Example

```python
num = 100
res = "202"

num = -7
res = "-10"
```



## 思路

就是一个取模和除法的问题

## 题解

```python
class Solution(object):
    def convertToBase7(self, num):
        if num == 0: return "0"  # 特殊情况特殊考虑
        posneg = ""  # 记录正负号
        if num < 0:
            posneg = posneg + '-'
            num = abs(num)
        number = ""
        while num != 0:
            number += str(num % 7)
            num = num // 7
        return posneg + number[::-1]
```



# 172. [Factorial Trailing Zeroes(Meduim)](https://leetcode.cn/problems/factorial-trailing-zeroes/)

## 题目

给定一个整数`n`，返回`n!`中尾随0的数量

## Example

```python
n = 3
res = 0 # 3! = 6, 尾巴没有0

n = 5
res = 1 # 5! = 120,最后有一个0
```

## 思路

每个尾部的 0 由 2 × 5 = 10 而来，因此把阶乘的每一个元素拆成质数相乘，统计有多少个 2 和 5即可。

很明显，质因子 2 的数量远多于质因子 5 的数量，因此我们可以只统计阶乘结果里有多少个质因子 5。

## 题解

```python
class Solution(object):
    def trailingZeroes(self, n):
        res = 0
        while n >= 5:
            res += n // 5
            n /= 5
        return res
```



# 随机与取样

# 528. [Random Pick with Weight (Medium)](https://leetcode.cn/problems/random-pick-with-weight/)

## 题目

给定一个正整数数组`w`，其中`w[i]`代表第`i`个下标对应的权重

要求实现一个函数`pickIndex`，可以按照上面的权重随机返回下标

## Example

```python
# 输入：
["Solution","pickIndex","pickIndex","pickIndex","pickIndex","pickIndex"]
[[[1,3]],[],[],[],[],[]]

# 输出：
[null,1,1,1,1,0]

"""
解释：
Solution solution = new Solution([1, 3]);
solution.pickIndex(); // 返回 1，返回下标 1，返回该下标概率为 3/4 。
solution.pickIndex(); // 返回 1
solution.pickIndex(); // 返回 1
solution.pickIndex(); // 返回 1
solution.pickIndex(); // 返回 0，返回下标 0，返回该下标概率为 1/4 。
"""
```

## 思路

比如有一个数组`w = [1, 2, 3, 4, 5, 6]`

我们先把它变成`accumulate = [1, 3, 6, 10, 15, 21]`

然后使用random.randint在`1~21`随机产生一个整数

比如产生了一个`11`，那我们返回`10`对应的下标，也就是`3`

为了加速，我们使用二分搜索来找这个下标

## 题解

```python
class Solution(object):
    def __init__(self, w):
        self.sum = sum(w)
        self.lst = [w[0]]
        for i in range(1, len(w)):
            self.lst.append(w[i]+self.lst[i-1])

    def pickIndex(self):
        target = random.randint(1, self.sum)
        l, r = 0, len(self.lst)
        while l < r:
            mid = (l + r) // 2
            if self.lst[mid] >= target:
                r = mid
            else:
                l = mid + 1
        return r

```

