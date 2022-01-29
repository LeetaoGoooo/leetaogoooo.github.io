---
title: Isomorphic Strings
tags: [Leetcode]

date: 2017-09-13
published: true
hideInList: false
feature: 
isTop: false
---







Given two strings s and t, determine if they are isomorphic.

Two strings are isomorphic if the characters in s can be replaced to get t.

All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character but a character may map to itself.

For example,
Given <code>"egg"</code>, <code>"add"</code>, return true.

Given <code>"foo"</code>, <code>"bar"</code>, return false.

Given <code>"paper"</code>, <code>"title"</code>, return true.

Note:
You may assume both **s** and **t** have the same length.

**Solution**

判断两个字符的结构是否一样:依次遍历两个字符中的元素获取其中索引,判断两个字符最后的索引值是否一致,如果一致则认为结构相同

Python

```python
class Solution(object):
    def isIsomorphic(self, s, t):
        """
        :type s: str
        :type t: str
        :rtype: bool
        """
        return [s.find(i) for i in s] == [t.find(j) for j in t]
```