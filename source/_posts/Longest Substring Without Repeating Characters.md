---
title: Longest Substring Without Repeating Characters
tags: [Leetcode]

date: 2018-10-25
published: true
hideInList: false
feature: 
isTop: false
---







给定一个字符串，找出不含有重复字符的最长子串的长度。

示例 1:

```
输入: "abcabcbb"
输出: 3 
解释: 无重复字符的最长子串是 "abc"，其长度为 3。
```

示例 2:

```
输入: "bbbbb"
输出: 1
解释: 无重复字符的最长子串是 "b"，其长度为 1。
```

示例 3:

```
输入: "pwwkew"
输出: 3
解释: 无重复字符的最长子串是 "wke"，其长度为 3。
     请注意，答案必须是一个子串，"pwke" 是一个子序列 而不是子串。
```

**Solution**

```python
class Solution:
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        if len(s) == 0:
            return 0

        max_len = 1
        store_str = ""

        for strs in s:
            if len(store_str) == 0:
                store_str += strs
            elif strs not in store_str:
                store_str += strs
                max_len = max(max_len,len(store_str))
            else:
                repeat_index = store_str.index(strs)
                store_str = store_str[repeat_index+1:] + strs
        return max_len
```