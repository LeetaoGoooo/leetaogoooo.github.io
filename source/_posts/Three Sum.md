---
title: Three Sum
tags: [Leetcode]

date: 2018-10-24
published: true
hideInList: false
feature: 
isTop: false
---







给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。

例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，

>满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]


**Solution**

思路很简单先计算两数之和，然后再找第三个数是否存在


```python
class Solution:
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        nums_hash = {}
        result = list()
        for num in nums:
            nums_hash[num] = nums_hash.get(num, 0) + 1
        if 0 in nums_hash and nums_hash[0] >= 3:
            result.append([0, 0, 0])

        neg = list(filter(lambda x: x < 0, nums_hash))
        pos = list(filter(lambda x: x>= 0, nums_hash))

        for i in neg:
            for j in pos:
                dif = 0 - i - j
                if dif in nums_hash:
                    if dif in (i, j) and nums_hash[dif] >= 2:
                        result.append([i, j, dif])
                    if dif < i or dif > j:
                        result.append([i, j, dif])
        return result
```