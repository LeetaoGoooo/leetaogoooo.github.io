---
title: Longest Palindrome
tags: [Leetcode]

date: 2017-09-05
published: true
hideInList: false
feature: 
isTop: false
---







Given a string which consists of lowercase or uppercase letters, find the length of the longest palindromes that can be built with those letters.

This is case sensitive, for example "Aa" is not considered a palindrome here.

**Note:**
Assume the length of given string will not exceed 1,010.

**Example:**

```
Input:
"abccccdd"

Output:
7

Explanation:
One longest palindrome that can be built is "dccaccd", whose length is 7.
```

**Solution**

最长回文字符串,区分大小写.依次统计每个字符出现的个数,对统计结果进行处理,很明显出现偶数次的字符串符合回文串的构成,对于大于2的奇数字符串,取它最大的2的倍数,然后将长度累加得出N,最后比较 **N** 和 字符串原始长度,如果长度一致则返回 **N**,如果小于原字符串长度则返回 **N + 1**

Python

```python
class Solution(object):
    def longestPalindrome(self, s):
        """
        :type s: str
        :rtype: int
        """
        dic_s = {}
        for item in s:
            dic_s[item] = dic_s.get(item,0) + 1
        max_len = 0
        for key in dic_s:
            if dic_s[key] >= 2:
                max_len += (dic_s[key] / 2) * 2
        if max_len < len(s):
            return max_len + 1
        return max_len
```