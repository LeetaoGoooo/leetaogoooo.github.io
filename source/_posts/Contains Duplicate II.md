---
title: Contains Duplicate II
tags: [Leetcode]

date: 2017-09-11
published: true
hideInList: false
feature: 
isTop: false
---







Given an array of integers and an integer k, find out whether there are two distinct indices i and j in the array such that **nums[i] = nums[j]** and the **absolute** difference between i and j is at most k.

**Solution**

找出列表中是否存在值相同的那个元素,并且他们的索引值的差值不大于 *k*,思路:用哈希表记录第一次元素A出现的位置,如果遍历出现第二个值相同的元素B,如果差值符合则返回 *True*, 否则将B的索引覆盖A(后面再出现相同的元素C索引值 *C-A* 肯定大于 *B-A*),一直没有返回 *False*

Python

```python
class Solution(object):
    def containsNearbyDuplicate(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: bool
        """
        dic_nums = {}
        
        for index,value in enumerate(nums):
            if value in dic_nums and (index - dic_nums[value] <= k) :
                return True
            else:
                dic_nums[value] = index
        return False 
```
