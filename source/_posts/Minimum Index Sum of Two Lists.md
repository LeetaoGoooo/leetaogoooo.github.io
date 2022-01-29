---
title: Minimum Index Sum of Two Lists
tags: [Leetcode]

date: 2017-08-27
published: true
hideInList: false
feature: 
isTop: false
---







Suppose Andy and Doris want to choose a restaurant for dinner, and they both have a list of favorite restaurants represented by strings.

You need to help them find out their common interest with the least list index sum. If there is a choice tie between answers, output all of them with no order requirement. You could assume there always exists an answer.

**Example 1:**

```
Input:
["Shogun", "Tapioca Express", "Burger King", "KFC"]
["Piatti", "The Grill at Torrey Pines", "Hungry Hunter Steakhouse", "Shogun"]
Output: ["Shogun"]
Explanation: The only restaurant they both like is "Shogun".
```

**Example 2:**

```
Input:
["Shogun", "Tapioca Express", "Burger King", "KFC"]
["KFC", "Shogun", "Burger King"]
Output: ["Shogun"]
Explanation: The restaurant they both like and have the least index sum is "Shogun" with index sum 1 (0+1).
```

Note:
The length of both lists will be in the range of [1, 1000].
The length of strings in both lists will be in the range of [1, 30].
The index is starting from 0 to the list length minus 1.
No duplicates in both lists.

**Solution**

Python

```python
class Solution(object):
    def findRestaurant(self, list1, list2):
        """
        :type list1: List[str]
        :type list2: List[str]
        :rtype: List[str]
        """
        dic_list1 = dict(zip(list1,[index for index in range(len(list1))]))
        res = []
        least_sum = None
        for index,value in enumerate(list2):
            if value in dic_list1.keys():
                dic_list1[value] += index
                if least_sum is None:
                    least_sum = dic_list1[value]
                    res.append(value)
                elif least_sum > dic_list1[value]:
                        least_sum = dic_list1[value]
                        res = []
                        res.append(value)
                elif least_sum == dic_list1[value]:
                    res.append(value)
        return res
```

如果不理解的话可以看讨论区一段代码 [Minimum Index Sum of Two Lists](https://discuss.leetcode.com/topic/90554/python-straightforward-with-explanation)

```python
def findRestaurant(self, A, B):
    Aindex = {u: i for i, u in enumerate(A)}
    best, ans = 1e9, []

    for j, v in enumerate(B):
        i = Aindex.get(v, 1e9)
        if i + j < best:
            best = i + j
            ans = [v]
        elif i + j == best:
            ans.append(v)
    return ans
```