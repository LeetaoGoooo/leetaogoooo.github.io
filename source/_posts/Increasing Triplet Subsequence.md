---
title: Increasing Triplet Subsequence
tags: [Leetcode]

date: 2018-10-29
published: true
hideInList: false
feature: 
isTop: false
---







给定一个未排序的数组，判断这个数组中是否存在长度为 3 的递增子序列。

数学表达式如下:
>如果存在这样的 i, j, k,  且满足 0 ≤ i < j < k ≤ n-1，<p>
使得 arr[i] < arr[j] < arr[k] ，返回 true ; 否则返回 false 。

**说明**: 要求算法的时间复杂度为 O(n)，空间复杂度为 O(1) 。

**示例 1**:

```
输入: [1,2,3,4,5]
输出: true
```

**示例 2**:

```
输入: [5,4,3,2,1]
输出: false
```

**Solution**

维护一个最大值，最小值的二元组，然后记录下前 i 个元素前的最小递增二元组，当存在元素大于最大值后即为 true


```python
class Solution:
    def increasingTriplet(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        max_value = float('inf')
        min_value = float('inf')
        for num in nums:
            if num > max_value:
                return True
            elif num <= max_value and num > min_value:
                max_value = num
            else:
                min_value = num
        return False
```