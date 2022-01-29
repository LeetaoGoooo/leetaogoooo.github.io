---
title: Longest Harmonious Subsequence
tags: [Leetcode]

date: 2017-08-28
published: true
hideInList: false
feature: 
isTop: false
---







We define a harmonious array is an array where the difference between its maximum value and its minimum value is exactly 1.

Now, given an integer array, you need to find the length of its longest harmonious subsequence among all its possible subsequences.

**Example 1:**

```
Input: [1,3,2,2,5,2,3,7]
Output: 5
Explanation: The longest harmonious subsequence is [3,2,2,2,3].
```

**Note**: The length of the input array will not exceed 20,000.

**Solution**

1. 统计每个数字出现的个数,生成 num——count 的字典
2. 遍历字典,判断 num + 1 的键是否存在字典中,存在则对比之前的最大长度,取最大值(default=0)

Python

```python
class Solution(object):
    def findLHS(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        dic_nums = {}
        for i in nums:
            dic_nums[i] = dic_nums.get(i,0) + 1
        
        max_len = 0
        for key in dic_nums:
            if (key + 1) in dic_nums:
                max_len = max(max_len, dic_nums[key] + dic_nums[key+1])
        return max_len
```

使用 *Counter*

```python
def findLHS(self, A):
    count = collections.Counter(A)
    ans = 0
    for x in count:
        if x+1 in count:
            ans = max(ans, count[x] + count[x+1])
    return ans
```