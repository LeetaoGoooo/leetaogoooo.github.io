---
title: max与min
tags: [Python]

date: 2019-03-05
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

对于 max 和 min 可以比较数值大小这一点大家肯定都是十分熟悉,除此之外 max 和 min 同样也可以比较 list 和 str 大小.

# 文档说明

首先看一下 max 和 min 的说明文档

```python
max(...)
    max(iterable, *[, default=obj, key=func]) -> value
    max(arg1, arg2, *args, *[, key=func]) -> value

    With a single iterable argument, return its biggest item. The
    default keyword-only argument specifies an object to return if
    the provided iterable is empty.
    With two or more arguments, return the largest argument.
   
 min(...)
    min(iterable, *[, default=obj, key=func]) -> value
    min(arg1, arg2, *args, *[, key=func]) -> value

    With a single iterable argument, return its smallest item. The
    default keyword-only argument specifies an object to return if
    the provided iterable is empty.
    With two or more arguments, return the smallest argument.  
```

从上面的文档不难看出,max 和 min 对 iterable 对象的支持,那么对 List 是如何比较的呢?看个例子


```python

list1, list2 = ['123', 'xyz', 'zara', 'abc'], [456, 700, 200]

print("Max value element : ", max(list1))
print("Max value element : ", max(list2))
```

输出结果如下:

```
Max value element :  zara
Max value element :  700
```

对于 List[int], max 的比较方式很容易发现, 直接比较列表中最大的那个数字, 那么对于 List[str] 的比较方式呢? c 语言中有一个 **strcmp** 的函数, max 的比较方式和它的方式类似, **两个字符串自左向右逐个字符相比（按ASCII值大小相比较），直到出现不同的字符,返回值大的字符**,min 比较方式也类似,只是返回值小的字符

# 彩蛋

之前在 Leetcode 看到一个题目,算是对 max,min 比较灵活的运用了,[
最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/),题目如下:


```
编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

示例 1:

输入: ["flower","flow","flight"]
输出: "fl"
示例 2:

输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
说明:

所有输入只包含小写字母 a-z 。
```

直接上答案:

```python
class Solution:
    def longestCommonPrefix(self, strs: 'List[str]') -> 'str':
        if not strs:
            return ''
        
        s1 = min(strs)
        s2 = max(strs)
        ans = ""
        
        for i, v in enumerate(s1):
            if v != s2[i]:
                return s1[:i]
                    
        return s1
```


