---
title: Python Leetcode101 字符串:KMP算法
date: 2023-02-08 21:31:00 +0800
categories: [Leetcode, String]
tags: [Algorithm]
render_with_liquid: false
---



[KMP算法](https://zh.wikipedia.org/zh-hans/KMP%E7%AE%97%E6%B3%95)由Knuth-Morris-Pratt三个人提出，用于查找字符串的出现位置。

适用于Leetcode 28. [Implement strStr](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

题目大概意思是从一个`haystack`字符串中找出`needle`字符串第一个匹配项的下标。

这个题本来程度是Medium，暴力求解即可。如果记大字符串的长度为`n`，待搜索字符串的长度为`m`，暴力时间复杂度是`O(m*n)`，而KMP算法可以把复杂度降到`O(m+n)`，实现线性复杂度。

我感觉如果题目要求必须上这个KMP算法实现线性复杂度的话，难度就能算上Hard了，毕竟KMP算法最开始提出是能发一篇论文的。

<img src="https://user-images.githubusercontent.com/84035000/222365330-fc852799-74e8-4dc4-bf18-6a506325516b.png" alt="image" style="zoom: 33%;" />

# 1 思路

首先约定一个表示方式，用`text`表达总字符串，`pattern`代表小字符串。给`text`安排一个指针`i`，给`pattern`安排一个指针`j`，用于移动匹配字符串。

KMP算法的设计在于：`i`只会往前走，`j`则可以根据一定的规则反复横跳，以「`i`永不后退」的方式实现线性时间复杂度。

如果知道了上述的总设计思想，我们不妨来猜测一下可行的做法。

## 1.1 Intuitive | 『 `i` 不动，`j` 退 』算法

怎么设计才能让`i`一直往前走呢？一个直觉性的想法是，每当遇到`text[i] != pattern[j]`的时候，我就让`i`不动，`j = 0`，继续往后搜，因为从`text[i]`那个位置起，肯定是匹配无望了。

以下面图为例，起初`i, j = 0, 0`，只要`text[i] == pattern[j]`，就`i+=1, j+=1`

<img src="https://user-images.githubusercontent.com/84035000/222372246-dac3f596-c542-4ecc-ae69-6bfbeca62861.png" alt="image" style="zoom:50%;" />

一直搜到`i=2, j=2`发现了mismatch

<img src="https://user-images.githubusercontent.com/84035000/222372751-20c1de2a-323f-40a1-8f3b-65b221168229.png" alt="image" style="zoom:50%;" />

这时候，根据我们的策略，`i`不动，`j`退回到0，然后继续往后搜，一直到搜索到解`text[4:]`。

<img src="https://user-images.githubusercontent.com/84035000/222374094-c3a35180-69fa-421a-bf53-b3402fe09e75.png" alt="image" style="zoom:50%;" />

这样看来，我们就跳过了`text[1]`也就是`"B"`这个位置的比较，节省了一点时间。

## 1.2 算法的局限和 j 的退位策略

虽然上面给出的那个示例是work的，但稍微变一变，就不行了。比如，`pattern`换成下面的：

<img src="https://user-images.githubusercontent.com/84035000/222374889-9af18bb9-ef3b-4d5d-ae05-5445e66f4f15.png" alt="image" style="zoom:50%;" />

在这一个例子下，`i=4, j=4`的时候出现了第一次mismatch，如果按照上面的方案，`i`不动，`j`退回到0的话，`text`后面就只剩两个字母`"BC"`了，那我们就没能搜到解。

但这个情况下是有解的，正解为`text[2:] = "ABABC" == pattern`，根据我们的算法，却搜不到。这说明，我们在搜索的过程中「跳过」了一些可能的情况。

为了搜索到所有可能的情况，我们需要变动一下`j`指针的策略。

不难发现，如果这个`j`不是退到`pattern[0]`，而是退到`pattern[2]`那个位置，同时`i`还是保留在4那个位置，我们就可以搜索到这个解。

那`j`是按照什么样的规则退回到`pattern[2]`的呢？

## 1.3 回退表单

如果我们把回退到`pattern[2]`的情况列在图上，应该就是如下图最后一行所示。我们可以根据这一行为归纳出一个策略。

<img src="https://user-images.githubusercontent.com/84035000/222402813-e471dcd7-3393-4063-baa5-4537dcb41e6c.png" alt="image" style="zoom:50%;" />

注意到，在`pattern`里面，`"AB"`这个组合连续出现了两次。当我们发现mismatch，想回退到`j=2`的时候，表示：我根据之前比较结果，发现`text[2 & 3]`对应`pattern[2 & 3]`时不可行，但它还可以对应`pattern[0 & 1]`，对应的字符串也都是`"AB"`，我可以从`j=3`开始，重新进行新一轮的比对。

更一般的，每次我们遇到在最后一个字符`pattern[4]='c'`无法匹配的时候，我们都可以回退到`j=2`去进行新一轮的比对。所以，我们应该为`pattern`制定一个「回退表单」，表示在哪里mismatch的时候，`j`回退到哪个地方比较合适。

# 2 制定回退表单

上面把算法的思路讲了一下，首先一步就是为`pattern`制定一个回退表单，以规定`j`的行为。这里具体介绍怎么指定回退表单`next`（这个next是paper里起的名字，但其实我觉得叫back也行）

## 2.1 最长相同前后缀

我们以这样一个字符串为例：`"a b c a b c"`

我们现在把它的前缀们列出来：`"a", "ab", "abc", 'abca', 'abcab'`

然后把它的后缀们列出来：`"d", "bd", "abc", "cabc", "bcabc"`

现在给这两个集合「前缀」和「后缀」求一个并集，看看有没有元素，发现有一个`"abc"`

那么，我们说：这个字符串的「最长相同前后缀」的长度为3

## 2.2 利用最长相同前后缀建立next

我们根据上面的定义，为上面的例子`"a b c a b c"`制定一个next表单，也就是填好下面这张表。其中`next[i]`表示：字符串`pattern[:i+1]`对应的「最长相同前后缀」长度。

| index / i  |  0   |  1   |  2   |  3   |  4   |  5   |
| :--------: | :--: | :--: | :--: | :--: | :--: | :--: |
| pattern[i] |  a   |  b   |  c   |  a   |  b   |  c   |
|  next[i]   |  0   |  0   |  0   |  1   |  2   |  3   |

首先，对于`i = 0`的情况，肯定有`next[0] = 0`，这是边界条件。

我们用一个`temp`变量存储当前的最长相同前后缀长度

如果当前字符`pattern[i] == pattern[temp]`，表示当前的字符能成为一个相同前后缀的开始或延续。这种情况下，我们就给`i,temp`分别加1，然后将`next[i] = temp`。

如果不等，且`temp == 0`，说明没啥好匹配的了，这个字符不属于相同前后缀，`next[i] = 0`

如果`temp > 0`，这种情况就要小心了，有可能`pattern[i]`不是`pattern[i-temp]`的延续，但很有可能还和`pattern[i-x]`有着延续关系，这个时候，我们令`temp = next[temp-1]`，继续搜索。这个情况不太好理解，我们可以用一个例子`"a a b a a a c"`来理解：

- 对于倒数第二个字符`a`，到这个地方的时候，`temp=2`，但是`pattern[i] != pattern[temp]`
- 我们令`temp = next[temp-1]`，此时有`temp = next[1] = 1 `
- 这样，我们就把`pattern[i] = 'a'`去和`pattern[1] = 'a'`比较，发现相等
- 于是`next[i] = temp + 1 = 2 `

## 2.3 建立next的代码

```python
def build_next(pattern):
    next = [0]
    i, temp = 1, 0
    while i < len(pattern):
        if pattern[temp] == pattern[i]:
            temp, i = temp+1, i+1
            next.append(temp)
        elif temp > 0:
            temp = next[temp-1]
        else:
            next.append(0)
            i += 1
    return next
```



# 3 KMP算法全过程

我们在2里建立了`next`表单，接下来就是利用这个表单实现`j`的回退。

具体怎么做呢，主循环肯定是要对`i`做的，那么对于每个`i`：

- 我们先比较`text[i]`是否和`pattern[j]`相等
  - 如果相等，继续往下搜索，`i, j = i+1, j+1`
  - 如果不相等
    - 有可能第一个字符串就失配了，这时候`j=0`，我们只需要`i+=1`即可
    - 有可能在匹配过程中失配，这时候`j > 0`，我们令`j = next[j-1]`
- 此外，在每次比较的时候，我们需要看`j`是不是已经到达`pattern`的长度了
  - 如果到达，说明匹配成功，可以返回了

具体代码如下：

```python
class Solution:
    def strStr(self, text: str, pattern: str) -> int:
        def build_next(pattern):
            next = [0]
            i, temp = 1, 0
            while i < len(pattern):
                if pattern[temp] == pattern[i]:
                    temp, i = temp+1, i+1
                    next.append(temp)
                elif temp > 0:
                    temp = next[temp-1]
                else:
                    next.append(0)
                    i += 1
            return next
        
        next = build_next(pattern) 
        i, j = 0, 0
        while i < len(text):
            if text[i] == pattern[j]:
                i, j = i+1, j+1
            elif j > 0:
                j = next[j-1]
            else:
                i += 1
            if j == len(pattern):
                return i-j
        return -1
```

这部分代码里面，`build_next`的过程和搜索过程有着惊人的相似性，可以把这个过程理解为，`pattern`自己在自己里面找字串。