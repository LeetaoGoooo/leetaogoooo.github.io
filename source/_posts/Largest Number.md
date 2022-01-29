---
title: Largest Number
tags: [Leetcode]

date: 2017-08-17
published: true
hideInList: false
feature: 
isTop: false
---








Given a list of non negative integers, arrange them such that they form the largest number.

For example, given <code>[3, 30, 34, 5, 9]</code>, the largest formed number is <code>9534330</code>.

Note: The result may be very large, so you need to return a string instead of an integer.

**Solution**

主要思路排序,这里需要意识到:<code>int(str(A) + str(B)) > int(str(B) + str(A))</code>,那么 A 应该在 B 之前,否则
B 应该在 A之前,注意对前导零的处理,*[0,0]* 结果应该是 "0"


Python

```python

# 归并排序

class Solution:
    # @param {integer[]} nums
    # @return {string}
    def largestNumber(self, num):
        num = [str(x) for x in num]
        num = self.mergeSort(num)
        return str(int("".join(num)))
    
    def mergeArray(self,left, right):
        """
            merge the left and right array
        """
        res = []
        i = j = 0
        while i < len(left) and j < len(right):
            if int(left[i] + right[j]) < int(right[j] + left[i]):
                res.append(right[j])
                j += 1
            else:
                res.append(left[i])
                i += 1
        while i < len(left):
            res.append(left[i])
            i += 1
        
        while j < len(right):
            res.append(right[j])
            j += 1
        return res
                
    def mergeSort(self,lists):
        if len(lists) <= 1:
            return lists
        mid = int(len(lists)/2)
        left = self.mergeSort(lists[:mid])
        right = self.mergeSort(lists[mid:])
        return self.mergeArray(left,right)
```

另外一种简洁的代码

```python
class Solution:
    # @param {integer[]} nums
    # @return {string}
    def largestNumber(self, num):
        num = [str(x) for x in num]
        num.sort(cmp=lambda x, y: cmp(y+x, x+y))
        return ''.join(num).lstrip('0') or '0'
```