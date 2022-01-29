---
title: Distribute Candies
tags: [Leetcode]

date: 2017-08-29
published: true
hideInList: false
feature: 
isTop: false
---







Given an integer array with even length, where different numbers in this array represent different kinds of candies. Each number means one candy of the corresponding kind. You need to distribute these candies equally in number to brother and sister. Return the maximum number of kinds of candies the sister could gain.


**Example 1:**

```
Input: candies = [1,1,2,2,3,3]
Output: 3
Explanation:
There are three different kinds of candies (1, 2 and 3), and two candies for each kind.
Optimal distribution: The sister has candies [1,2,3] and the brother has candies [1,2,3], too. 
The sister has three different kinds of candies.
```

**Example 2:**

```
Input: candies = [1,1,2,3]
Output: 2
Explanation: For example, the sister has candies [2,3] and the brother has candies [1,1]. 
The sister has two different kinds of candies, the brother has only one kind of candies.
```

**Note:**

1. The length of the given array is in range [2, 10,000], and will be even.
2. The number in given array is in range [-100,000, 100,000].


**Solution**

给兄妹分糖果，糖果数量表示为偶数长度的数组，不同的数字代表不同的糖果，每个数字代表一颗糖果。现要求兄妹分得的糖果数量一样多，但是妹妹的种类必须尽量多。返回妹妹获得的糖果种类数。

若种类大于长度一半,则妹妹得到的种类为长度的一半,若种类小于长度一半,则返回种类的长度


Python

```python
class Solution(object):
    def distributeCandies(self, candies):
        """
        :type candies: List[int]
        :rtype: int
        """
        set_len = len(set(candies))
        return min(set_len,len(candies)/2)
```