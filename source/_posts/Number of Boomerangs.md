---
title: Number of Boomerangs
tags: [Leetcode]

date: 2017-08-31
published: true
hideInList: false
feature: 
isTop: false
---







Given n points in the plane that are all pairwise distinct, a "boomerang" is a tuple of points <code>(i, j, k)</code> such that the distance between <code>i</code> and <code>j</code> equals the distance between <code>i</code> and <code>k</code> (the order of the tuple matters).

Find the number of boomerangs. You may assume that n will be at most **500** and coordinates of points are all in the range **[-10000, 10000]** (inclusive).

**Example:**

```
Input:
[[0,0],[1,0],[2,0]]

Output:
2

Explanation:
The two boomerangs are [[1,0],[0,0],[2,0]] and [[1,0],[2,0],[0,0]]
```

**Solution**

这题标签是 *hash table* 很明显最佳解法是利用 *hash table*,依次判断一个点与其他点的距离,如果存在相同距离并且个数不小于2的点(N),任意选择两个点则可以组成回旋镖,排列组合:A(n,m) = n(n-1)(n-2)...(n-m+1),从N中选择两点:A(N,2) = N(N-1),然后累加


Python

```python
class Solution(object):
    def numberOfBoomerangs(self, points):
        """
        :type points: List[List[int]]
        :rtype: int
        """
        res = 0
        for x1,y1 in points:
            dis_map = {}
            for x2,y2 in points:
                dis_map[(x1-x2)**2 + (y1-y2)**2] = 1 + dis_map.get((x1-x2)**2 + (y1-y2)**2,0)
            for dis in dis_map: #1
                res += dis_map[dis] * (dis_map[dis] - 1)
        return res
```

简单思考一下:如果增加一个距离相等的点,多出来的组合个数,应该为 *(N+1)((N+1)-1) - N(N-1) = 2N*,则代码可以修改为:


```python
class Solution(object):
    def numberOfBoomerangs(self, points):
        """
        :type points: List[List[int]]
        :rtype: int
        """
        count = 0
        for point1 in points:
            m = {}
            for point2 in points:
                dx = point1[0] - point2[0]
                dy = point1[1] - point2[1]
                d = dx*dx + dy*dy
                if d in m:
                    count += m[d]*2
                    m[d] +=1
                else:
                    m[d] = 1
        return count
```

