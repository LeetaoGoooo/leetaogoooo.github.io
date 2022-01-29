---
title: Longest Word in Dictionary through Deleting
tags: [Leetcode]

date: 2017-08-19
published: true
hideInList: false
feature: 
isTop: false
---







Given a string and a string dictionary, find the longest string in the dictionary that can be formed by deleting some characters of the given string. If there are more than one possible results, return the longest word with the smallest lexicographical order. If there is no possible result, return the empty string.

**Example 1:**

```
Input:
s = "abpcplea", d = ["ale","apple","monkey","plea"]

Output: 
"apple"
```

**Example 2:**

```
Input:
s = "abpcplea", d = ["a","b","c"]

Output: 
"a"
```

**Note:**

1. All the strings in the input will only contain lower-case letters.
2. The size of the dictionary won't exceed 1,000.
3. The length of all the strings in the input won't exceed 1,000.

**Solutions**

通过删除字符串去形成字典中的单词,保持原有字符串的相对顺序,对于结果优先考虑最长的字符串,长度相等的情况下考虑字典序最小的字符串

Python

```python
class Solution(object):
    def findLongestWord(self, s, d):
        """
        :type s: str
        :type d: List[str]
        :rtype: str
        """
        res_list = []
        def isSubstring(sub, source):
            for item in sub:
                if source.find(item) != -1:
                    j = source.find(item)
                    source = source[j+1:]
                else:
                    return False      
            return True

        for item in d:
            if(isSubstring(item, s)):
                res_list.append(item)

        res = ""
        max_len = 0

        for item in res_list:
            if len(item) > max_len or (len(item) == max_len and item < res):
                res = item
                max_len = len(item)
                
        return res        
```

比较简洁的 Python 代码

```python
def findLongestWord(self, s, d):
    def isSubsequence(x):
        it = iter(s)
        return all(c in it for c in x)
    return max(sorted(filter(isSubsequence, d)) + [''], key=len)
```

Java

```java
public class Solution {
    public String findLongestWord(String s, List<String> d) {
        String res = "";
        if (s.length() == 0 || d.size() == 0) {
            return res;
        }
        for (String str : d) {
            int resLen = res.length();
            int strLen = str.length();
            if (isSequence(s, str) &&
                (strLen > resLen || (strLen == resLen && str.compareTo(res) < 0))) {
                res = str;
            }
        }
        return res;
    }
    
    private boolean isSequence(String s, String d) {
        int i = 0;
        int j = 0;
        while (i < s.length() && j < d.length()) {
            while (i < s.length() && s.charAt(i) != d.charAt(j)) {
                i ++;
            }
            if (i < s.length()) {
                i ++;
                j ++;
            }
        }
        return j == d.length();
    }
}
```

简洁版的 Java 代码

```java
public String findLongestWord(String s, List<String> d) {
    String longest = "";
    for (String dictWord : d) {
        int i = 0;
        for (char c : s.toCharArray()) 
            if (i < dictWord.length() && c == dictWord.charAt(i)) i++;

        if (i == dictWord.length() && dictWord.length() >= longest.length()) 
            if (dictWord.length() > longest.length() || dictWord.compareTo(longest) < 0)
                longest = dictWord;
    }
    return longest;
}
```