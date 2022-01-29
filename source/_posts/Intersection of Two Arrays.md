---
title: Intersection of Two Arrays
tags: [Leetcode]

date: 2017-08-14
published: true
hideInList: false
feature: 
isTop: false
---







Given two arrays, write a function to compute their intersection.

**Example**:
Given nums1 = <code>[1, 2, 2, 1]</code>, nums2 = <code>[2, 2]</code>, return <code>[2]</code>.

**Note**:
1. Each element in the result must be unique.
2. The result can be in any order.


**solutions**

Python

```python
class Solution(object):
    def intersection(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: List[int]
        """
        nums1 = set(nums1)
        nums2 = set(nums2)
        return list(nums1&nums2)
```


Java

```java
public class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set = new HashSet<>();
        Arrays.sort(nums2);
        for (Integer num: nums1) {
            if (binarySearch(nums2, num)) {
                set.add(num);
            }
        }
        int i = 0;
        int[] res = new int[set.size()];
        for (Integer num : set) {
            res[i++] = num;
        }
        return res;
    }
    
    public boolean binarySearch(int[] nums, int target) {
        int low = 0;
        int high = nums.length - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (nums[mid] == target) {
                return true;
            }
            if (nums[mid] > target) {
                high = mid - 1;
            } else{
                low = mid + 1;
            }
        }
        return false;
    }
}




// recommended
public class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        if (nums1 == null || nums2 == null) {
            return null;
        }

        Arrays.sort(nums1);
        Arrays.sort(nums2);
        int i = 0, j = 0, index = 0;
        int[] temp = new int[nums1.length];
        while (i < nums1.length && j < nums2.length) {
        	if (nums1[i] == nums2 [j]) {
        		if (index == 0 || temp[index - 1] != nums1[i]) {
        			temp[index] = nums1[i];
        			index ++;
        		}
        		i ++;
        		j ++;
        	} else if (nums1[i] < nums2[j]){
        		i ++;
        	} else {
        		j ++;
        	}
        }
        int[] result = new int[index];
        for(int k = 0; k < index; k++) {
            result[k] = temp[k];
        }
        return result;
    }
}
```