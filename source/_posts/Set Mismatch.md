---
title: Set Mismatch
tags: [Leetcode]

date: 2017-09-14
published: true
hideInList: false
feature: 
isTop: false
---







The set <code>S</code> originally contains numbers from 1 to <code>n</code>. But unfortunately, due to the data error, one of the numbers in the set got duplicated to another number in the set, which results in repetition of one number and loss of another number.

Given an array <code>nums</code> representing the data status of this set after the error. Your task is to firstly find the number occurs twice and then find the number that is missing. Return them in the form of an array.

**Example 1:**

```shell
Input: nums = [1,2,2,4]
Output: [2,3]
```

**Note:**
1. The given array size will in the range [2, 10000].
2. The given array's numbers won't have any order.

**Solution**

初始化一个长度为 len(nums) + 1的列表 A,然后遍历列表 nums,统计该列表各个元素出现的个数并记录在 A 中,然后统计列表 A 元素值为 2 的索引和 0 索引即为我们所求的值

Python

```python
class Solution(object):
    def findErrorNums(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        len_num = len(nums)
        count = [0] * (len(nums)+1)
        for num in nums:
            count[num] += 1
        for i in range(1,len(nums)+1):
            if count[i] == 2:
                a = i
            if count[i] == 0:
                b = i
        return [a,b]
```