---
title: Leetcode-572 另一个树的子树
tags: [Leetcode,树]

date: 2020-05-07
published: true
hideInList: false
feature: 
isTop: false
---



给定两个非空二叉树 s 和 t，检验 s 中是否包含和 t 具有相同结构和节点值的子树。s 的一个子树包括 s 的一个节点和这个节点的所有子孙。s 也可以看做它自身的一棵子树。

示例 1:
给定的树 s:

```
     3
    / \
   4   5
  / \
 1   2
 
```

给定的树 t：

```
   4 
  / \
 1   2
```

返回 true，因为 t 与 s 的一个子树拥有相同的结构和节点值。

示例 2:
给定的树 s：

```
     3
    / \
   4   5
  / \
 1   2
    /
   0
```

给定的树 t：

```
   4
  / \
 1   2
``` 

返回 false。

# 思路

这题是让我们判断一个树是否是另外一个树的子树，从题目的一开始**s 的一个子树包括 s 的一个节点和这个节点的所有子孙**结合第二个例子不难发现，中间的某个部分不能算是子树。那么就相当于**需要判断s的所有子树中是否和t有一样的**，最后就演变成**判断两个树是否相等了**。判断俩个树是否相等，可以用递归的写法。


## 判断两个树是否相等

```python
def isSameTree(s: TreeNode, t: TreeNode) -> bool:
    if not s and not t:
        return True
    if not s or not t:
        return False
    if s.val != t.val:
        return False
    return isSameTree(s.left,t.left) and isSameTree(s.right,t.right)
```


# 解法

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isSubtree(self, s: TreeNode, t: TreeNode) -> bool:
        def isSameTree(s: TreeNode, t: TreeNode) -> bool:
            if not s and not t:
                return True
            if not s or not t:
                return False
            if s.val != t.val:
                return False
            return isSameTree(s.left,t.left) and isSameTree(s.right,t.right)
        if not s:
            return False
        if isSameTree(s,t):
            return True
        return self.isSubtree(s.left,t) or self.isSubtree(s.right,t)
```


# 开放思路

如果我们把两个树的所有节点值都保存到两个字符串 s' 和 t' 中，然后判断是否 t' 在 s' 中就能得出 t 是否是 s 的子树了？

答案是肯定的，有兴趣的可以按照这个思路去实践一下。