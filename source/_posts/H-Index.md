---
title: H-Index
tags: [Leetcode]

date: 2017-09-06
published: true
hideInList: false
feature: 
isTop: false
---







Given an array of citations (each citation is a non-negative integer) of a researcher, write a function to compute the researcher's h-index.

According to the definition of h-index on Wikipedia: "A scientist has index h if h of his/her N papers have **at least** h citations each, and the other N − h papers have **no more than** h citations each."

For example, given citations = <code>[3, 0, 6, 1, 5]</code>, which means the researcher has <code>5</code> papers in total and each of them had received <code>3, 0, 6, 1, 5</code> citations respectively. Since the researcher has <code>3</code> papers with at least <code>3</code> citations each and the remaining two with no more than <code>3</code> citations each, his h-index is </code>3</code>.

**Note**: If there are several possible values for <code>h</code>, the maximum one is taken as the h-index.

**Solution**

需要明白h-index指数是怎么计算的,可以参考上面给的链接:
[Wikipediap--h-index](https://en.wikipedia.org/wiki/H-index)


Python

```python
class Solution(object):
    def hIndex(self, citations):
        """
        :type citations: List[int]
        :rtype: int
        """
        citations = sorted(citations,reverse=True)
        for index,value in enumerate(citations):
            if index+1 > value:
                return index
        return len(citations)
```

Java

```java

// 来自讨论区

public int hIndex(int[] citations) {
    int n = citations.length;
    int[] buckets = new int[n+1];
    for(int c : citations) {
        if(c >= n) {
            buckets[n]++;
        } else {
            buckets[c]++;
        }
    }
    int count = 0;
    for(int i = n; i >= 0; i--) {
        count += buckets[i];
        if(count >= i) {
            return i;
        }
    }
    return 0;
}
```