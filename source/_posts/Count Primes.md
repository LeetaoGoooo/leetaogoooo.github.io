---
title: Count Primes
tags: [Leetcode]

date: 2017-09-10
published: true
hideInList: false
feature: 
isTop: false
---








**Description:**

Count the number of prime numbers less than a non-negative number, n.

**Solution**

统计在[0, n) 区间内中质数的个数,按照常规方法去计算是否为素数可能会超时(没有尝试),可以用 **素数筛法**

>用筛法求素数的基本思想是：把从1开始的、某一范围内的正整数从小到大顺序排列， 1不是素数，首先把它筛掉。剩下的数中选择最小的数是素数，然后去掉它的倍数。依次类推，直到筛子为空时结束。 --[筛法求素数](https://baike.baidu.com/item/%E7%AD%9B%E6%B3%95%E6%B1%82%E7%B4%A0%E6%95%B0/8670409?fr=aladdin&fromid=15458451&fromtitle=%E7%B4%A0%E6%95%B0%E7%AD%9B%E6%B3%95#5)

如下图:
![](http://ww1.sinaimg.cn/large/006wYWbGly1fjehvrh7b6g30cd0a9q6x.gif)

Python

```python
class Solution(object):
    def countPrimes(self, n):
        """
        :type n: int
        :rtype: int
        """
        if n < 3:
            return 0
        primes = [True] * n
        for i in range(2, int(n**0.5)+1):
            j = i
            k = j
            while j*k < n:
                primes[j * k ] = False
                k += 1
        
        count_primes = 0 // [1]
        
        for i in range(2, n):
            if primes[i]:
                count_primes += 1
        
        return count_primes
        
```


从 [1] 到最后的代码可以改为:

```python
return sum(primes) - 2 // 0,1需要去掉
```

有个感觉很不错的写法:

```python
class Solution:
# @param {integer} n
# @return {integer}
def countPrimes(self, n):
    if n < 3:
        return 0
    primes = [True] * n
    primes[0] = primes[1] = False
    for i in range(2, int(n ** 0.5) + 1):
        if primes[i]:
            primes[i * i: n: i] = [False] * len(primes[i * i: n: i])
    return sum(primes)
```
