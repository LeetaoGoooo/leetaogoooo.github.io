---
title: Wiggle Sort II
tags: [Leetcode]

date: 2017-08-24
published: true
hideInList: false
feature: 
isTop: false
---







Given an unsorted array nums, reorder it such that <code>nums[0] < nums[1] > nums[2] < nums[3]....</code>

**Example:**
(1) Given nums = <code>[1, 5, 1, 1, 6, 4]</code>, one possible answer is <code>[1, 4, 1, 5, 1, 6]</code>. 
(2) Given nums = <code>[1, 3, 2, 2, 3, 1]</code>, one possible answer is <code>[2, 3, 1, 3, 1, 2]</code>.

**Note:**
You may assume all input has valid answer.

**Follow Up:**
Can you do it in O(n) time and/or in-place with O(1) extra space?

**Solutions**

1. 将列表进行排序
2. 找到排序后的中位数
3. 以中位数将列表分为前后两个部分A,B
4. 依次逆序输出A,B中的结果集

Python

```python
class Solution(object):
    def wiggleSort(self, nums):
        """
        :type nums: List[int]
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        sorted_nums = sorted(nums)
        half = len(sorted_nums[::2])
        nums[0::2],nums[1::2] = sorted_nums[0:half][::-1],sorted_nums[half:][::-1]
```
