---
title: Set Matrix Zeroes
tags: [Leetcode]

date: 2018-10-25
published: true
hideInList: false
feature: 
isTop: false
---







给定一个 m x n 的矩阵，如果一个元素为 0，则将其所在行和列的所有元素都设为 0。请使用原地算法。

示例 1:

>输入: <p>
[<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[1,1,1],<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[1,0,1],<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[1,1,1]<p>
]<p>
输出:<p> 
[<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[1,0,1],<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[0,0,0],<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[1,0,1]<p>
]<p>

示例 2:<p>
>输入: <p>
[<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[0,1,2,0],<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[3,4,5,2],<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[1,3,1,5]<p>
]<p>
输出: <p>
[<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[0,0,0,0],<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[0,4,5,0],<p>
  &nbsp;&nbsp;&nbsp;&nbsp;[0,3,1,0]<p>
]

进阶:

一个直接的解决方案是使用  O(mn) 的额外空间，但这并不是一个好的解决方案。
一个简单的改进方案是使用 O(m + n) 的额外空间，但这仍然不是最好的解决方案。
你能想出一个常数空间的解决方案吗？

**Solution**

```python
class Solution:
    def setZeroes(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: void Do not return anything, modify matrix in-place instead.
        """
        zero_pos_list = []
        
        matrix_row_len = len(matrix)
        matrix_col_len = len(matrix[0])
        
        for row in range(matrix_row_len):
            for col in range(matrix_col_len):
                if matrix[row][col] == 0:
                    zero_pos_list.append([row,col])

        for row,col in zero_pos_list:
            for index in range(matrix_col_len):
                matrix[row][index] = 0
            for index in range(matrix_row_len):
                matrix[index][col] = 0
```

O(1) 的解法

```python
import itertools

class Solution:
    def setZeroes(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: void Do not return anything, modify matrix in-place instead.
        """
        if not matrix:
            return
        
        R, C = len(matrix), len(matrix[0])
        
        # First pass: Set rows/cols to infinity when you encounter 0
        for r, c in itertools.product(range(R), range(C)):
            if matrix[r][c] == 0:
                for i in range(R):
                    if i == r or matrix[i][c] == 0:
                        continue
                    matrix[i][c] = float('inf')
                    
                for j in range(C):
                    if j == c or matrix[r][j] == 0:
                        continue
                    matrix[r][j] = float('inf')
                
        # Second pass: Replace all infinity with 0
        for r, c in itertools.product(range(R), range(C)):
            if matrix[r][c] == float('inf'):
                matrix[r][c] = 0
```