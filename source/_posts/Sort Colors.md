---
title: Sort Colors
tags: [Leetcode]

date: 2017-08-15
published: true
hideInList: false
feature: 
isTop: false
---







Given an array with n objects colored red, white or blue, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

**Note**:

You are not suppose to use the library's sort function for this problem.


**Solution**

[计数排序](https://zh.wikipedia.org/zh-cn/%E8%AE%A1%E6%95%B0%E6%8E%92%E5%BA%8F)


python

```python
class Solution(object):
    def sortColors(self, nums):
        """
        :type nums: List[int]
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        zeros = 0
        ones = 0
        twos = 0
        for num in nums:
            if num == 0:
                zeros += 1
            if num == 1:
                ones += 1
            if num == 2:
                twos += 1
            
        for i in range(zeros):
            nums[i] = 0
        for i in range(ones):
            nums[zeros + i] = 1
        for i in range(twos):
            nums[zeros + ones + i] = 2
```

Java

```java
	public static void sortColors(int []nums){
		int red=0,white=0,blue=0;
		for(int i=0;i!=nums.length;i++){//first, count their numbers
			if(nums[i]==0){
				red++;
			}else if(nums[i]==1){
				white++;
			}else{
				blue++;
			}
		}
		for(int i=0;i!=nums.length;i++){//second, rewrite the original array
			if(i<red){
				nums[i]=0;
			}else if(i<white+red){
				nums[i]=1;
			}else{
				nums[i]=2;
			}
		}
	}
```