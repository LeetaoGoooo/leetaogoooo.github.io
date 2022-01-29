---
title: Find All Anagrams in a String
tags: [Leetcode]

date: 2017-08-11
published: true
hideInList: false
feature: 
isTop: false
---







Given a string **s** and a **non-empty** string **p**, find all the start indices of **p**'s anagrams in **s**.

Strings consists of lowercase English letters only and the length of both strings **s** and **p** will not be larger than 20,100.

The order of output does not matter.

**Example 1**:

```shell
Input:
s: "cbaebabacd" p: "abc"

Output:
[0, 6]

Explanation:
The substring with start index = 0 is "cba", which is an anagram of "abc".
The substring with start index = 6 is "bac", which is an anagram of "abc".
```

**Example 2**:

```shell
Input:
s: "abab" p: "ab"

Output:
[0, 1, 2]

Explanation:
The substring with start index = 0 is "ab", which is an anagram of "ab".
The substring with start index = 1 is "ba", which is an anagram of "ab".
The substring with start index = 2 is "ab", which is an anagram of "ab".
```

**Solution**

```python
from collections import Counter
class Solution(object):
    def findAnagrams(self, s, p):
        """
        :type s: str
        :type p: str
        :rtype: List[int]
        """
        res  = []
        p_len = len(p)
        s_len = len(s)
        p_counter = Counter(p)
        s_tmp_counter = Counter(s[:p_len])
        if s_tmp_counter == p_counter:
            res.append(0)
        for i in range(p_len,s_len):
            s_tmp_counter[s[i-p_len]] -= 1
            if s_tmp_counter[s[i-p_len]] == 0:
                del s_tmp_counter[s[i-p_len]]
            s_tmp_counter[s[i]] += 1
            if s_tmp_counter == p_counter:
                res.append(i+1-p_len)
        return res
```