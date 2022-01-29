---
title: Intersection of Two Arrays II
tags: [Leetcode]

date: 2017-09-08
published: true
hideInList: false
feature: 
isTop: false
---






Given two arrays, write a function to compute their intersection.

**Example:**

Given nums1 = <code>[1, 2, 2, 1]</code>, nums2 = <code>[2, 2]</code>, return <code>[2, 2]</code>.

**Note:**
1. Each element in the result should appear as many times as it shows in both arrays.
2. The result can be in any order.

**Follow up:**

1. What if the given array is already sorted? How would you optimize your algorithm?
2. What if nums1's size is small compared to nums2's size? Which algorithm is better?
3. What if elements of nums2 are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?

**Solution**

统计两个列表重叠部分,统计两个列表中任意一个各个元素出现的个数保存在字典中(key-value),然后遍历另外一个列表,如果该元素存在字典中并且value不为0,则将其保存到结果集中,同事将对应的字典中的value减一,最后输出结果集.


```python
class Solution(object):
    def intersect(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: List[int]
        """
        if len(nums2) > len(nums1):
            nums2, nums1 = nums1, nums2
        nmap = {}
        for i in nums2:
            if i not in nmap:
                nmap[i] = 1
            else:
                nmap[i] += 1
        ans = []
        for i in nums1:
            if i in nmap and nmap[i] > 0:
                nmap[i] -= 1
                ans.append(i)
        return ans
                    
```

如果不用字典的话,遍历其中一个列表 A 的各个元素,如果这个元素存在于另外一个列表 B 中,将其保存到结果集后移除该元素在列表 B 中第一次出现的位置,最后输出结果集

```python
class Solution(object):
    def intersect(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: List[int]
        """
        res = []
        for num in nums2:
            if num in nums1:
                res.append(num)
                index = nums1.index(num)
                del nums1[index]
        return res
                
```