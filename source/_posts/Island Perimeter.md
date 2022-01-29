---
title: Island Perimeter
tags: [Leetcode]

date: 2017-08-30
published: true
hideInList: false
feature: 
isTop: false
---







You are given a map in form of a two-dimensional integer grid where 1 represents land and 0 represents water. Grid cells are connected horizontally/vertically (not diagonally). The grid is completely surrounded by water, and there is exactly one island (i.e., one or more connected land cells). The island doesn't have "lakes" (water inside that isn't connected to the water around the island). One cell is a square with side length 1. The grid is rectangular, width and height don't exceed 100. Determine the perimeter of the island.

**Example:**

```
[[0,1,0,0],
 [1,1,1,0],
 [0,1,0,0],
 [1,1,0,0]]

Answer: 16
Explanation: The perimeter is the 16 yellow stripes in the image below:
```

![](http://ww1.sinaimg.cn/large/006wYWbGly1fj20brsni7j306505xwe9.jpg)

**Solution**

找出公用的边个数m,以及1的个数n,然后结果为 <code>4*n - m</code>,这题的标签是 *hash table*, 不过并没有发现好的 *hash table* 的做法 

Python

```python
class Solution(object):
    def islandPerimeter(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """
        def intersect(i ,j):
            intersection = 0
            intersection += 1 if (i - 1 >= 0 and grid[i-1][j] == 1) else 0  # top
            intersection += 1 if (j - 1 >= 0 and grid[i][j-1] == 1 ) else 0 # left
            intersection += 1 if (j + 1 < len(grid[0]) and grid[i][j+1] == 1) else 0 # right
            intersection += 1 if (i + 1 < len(grid) and grid[i+1][j] == 1) else 0 # bottom
            return intersection
        
        res = 0
        
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if grid[i][j] == 1:
                    res += 4 - intersect(i,j)
        return res
```