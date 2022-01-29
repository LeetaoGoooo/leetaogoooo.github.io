---
title: Keyboard Row
tags: [Leetcode]

date: 2017-08-30
published: true
hideInList: false
feature: 
isTop: false
---







Given a List of words, return the words that can be typed using letters of alphabet on only one row's of American keyboard like the image below.

![](http://ww1.sinaimg.cn/large/006wYWbGly1fj20ftjrcbj30m807fdgk.jpg)

**Example 1:**

```
Input: ["Hello", "Alaska", "Dad", "Peace"]
Output: ["Alaska", "Dad"]
```

**Note:**

1. You may use one character in the keyboard more than once.
2. You may assume the input string will only contain letters of alphabet.

**Solution**

水题,看懂题意就能做出来了,判断单词能不能用键盘上一行打出

Python

```python
class Solution(object):
    def findWords(self, words):
        """
        :type words: List[str]
        :rtype: List[str]
        """
        return filter(re.compile('(?i)([qwertyuiop]*|[asdfghjkl]*|[zxcvbnm]*)$').match, words)
```