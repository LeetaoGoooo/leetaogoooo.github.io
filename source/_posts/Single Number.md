---
title: Single Number
tags: [Leetcode]

date: 2017-09-06
published: true
hideInList: false
feature: 
isTop: false
---







Given an array of integers, every element appears twice except for one. Find that single one.

**Note:**

Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?


**Solution**

O(n)算法其实不难,主要还得做到空间复杂度为O(1).

**O(n)的算法**

这题的标签是 *hash table*,很明显常规解法就是使用这个:

1. 遍历列表然后用字典统计**数字--数字出现的个数**
2. 遍历字典返回 value 值为 1 的数字

当然很明显这种做法,空间复杂度超过 O(1) 了

```python
class Solution(object):
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        dic_nums = {}
        for num in nums:
            dic_nums[num] = dic_nums.get(num,0) + 1
        
        for key in dic_nums:
            if dic_nums[key] == 1:
                return key
```

**时间复杂度O(n)和空间复杂度O(1)**

再说具体算法之前先铺垫一下:

>异或，英文为exclusive OR，或缩写成xor. 
如果a、b两个值不相同，则异或结果为1.如果a、b两个值相同,异或结果为0.

结合题意,列表中只有一个数字是single的,其它的都是成对的,成对的结果总是为0,将列表所有值异或结果应该是: *0^0^...^single = single*,
时间复杂度和空间复杂度都符合要求,算法如下:

```python
class Solution(object):
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        res = 0
        for num in nums:
            res ^= num
        return res
```