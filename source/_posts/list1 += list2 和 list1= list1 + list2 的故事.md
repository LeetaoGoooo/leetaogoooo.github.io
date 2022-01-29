---
title: list1 += list2 和 list1= list1 + list2 的故事
tags: [Python]

date: 2018-12-14
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

前几天在某乎上有人问问题,问道 list.append(ele) 和 list = list + [ele] 一样吗? 这个问题的答案自然是不一样的,这里就不过多描述了.重要的是,借由这个问题,我产生了一个新的问题: **list1 += list2 和 list1= list1 + list2  这两者有区别吗** 先说结论,自然是有区别的.

# 测试

花一分钟看个两个例子,如果你能正确的知道输出结果,那么这篇文章最重要的部分你已经掌握了,可以没有必要通读全文了.

## Example00

```python
list1 = [1,2,3]
list2 = list1
list1 += [4,5,6]

print(list1)
print(list2)
```

## Example01

```python
list1 = [1,2,3] 
list2 = list1 
list1 = list1 + [4,5,6] 
 
print(list1) 
print(list2) 
```

# 输出

让我们看一下这两个例子的输出

## Example00

```
[1, 2, 3, 4, 5, 6]
[1, 2, 3, 4, 5, 6]
```

## Example01

```
[1, 2, 3, 4, 5, 6]
[1, 2, 3]
```

# 原因

你可以将 "+=" 看作 extend, 它在原有的 list 基础上进行元素的拓充, 而 list = list + [ele] 则是,重新创建了一个 list 然后将原来的 list 和新添加的元素添加进去.为了帮助理解,我画了两张动图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fy42h5v76ng30hs0dcwh9.gif)
![](http://ww1.sinaimg.cn/large/006wYWbGly1fy42hm5oj5g30hs0dcq6t.gif)