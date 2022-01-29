---
title: Dict 键值和 Number 的故事
tags: [Python,Python标准库]

date: 2018-12-31
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

字典(Dict)，在大家写 Python 程序中，肯定会经常用到它，但是它在官方的文档有一段话，不知道大家有没有注意到，官方原文是这样的

> A dictionary’s keys are almost arbitrary values. Values that are not hashable, that is, values containing lists, dictionaries or other mutable types (that are compared by value rather than by object identity) may not be used as keys. Numeric types used for keys obey the normal rules for numeric comparison: if two numbers compare equal (such as 1 and 1.0) then they can be used interchangeably to index the same dictionary entry. (Note however, that since computers store floating-point numbers as approximations it is usually unwise to use them as dictionary keys.)

如果你看到这里已经明白了我想说的内容的话，那么恭喜你可以节约几分钟的时间了，不需要阅读本篇文章了，没看明白的小伙伴，可以接着看下面的例子。

# 例子

```bash
>>> a = {}
>>> a[1.0]="元旦快乐"
>>> a[1] = "新年快乐"
# a[1.0] = ?
```
问题来了，这时候我们试着输出 **a[1.0]** 会发生什么呢？

![](https://t1.picb.cc/uploads/2018/12/31/Jo1ch0.png)


#  原因

是不是觉得很神奇？这是为什么呢？划重点的时候到了,注意上面的官方文档中的一句话。

> Numeric types used for keys obey the normal rules for numeric comparison: if two numbers compare equal (such as 1 and 1.0) then they can be used interchangeably to index the same dictionary entry.

其意思很简单，用于键数字类型遵循正常数值比较规则：如果两个数比较相等（如1和1.0），那么他们可以互换使用，以索引相同的字典条目。

等等，这是不是就意味着 **1 == 1.0 ?**,真的是这样吗？别着急，看下去。

```bash
>>> 1 == 1.0
True
>>> hash(1) == hash(1.0)
True
```

所以这就不难理解，为什么 a[1.0] 的值，被后面的 a[1] 覆盖了。最后官方也建议，不要用他们作为字典的键值。


# 参考链接

[Mapping Types -- dict](https://docs.python.org/3.6/library/stdtypes.html#mapping-types-dict)

[Why can a floating point dictionary key overwrite an integer key with the same value?](https://stackoverflow.com/questions/32209155/why-can-a-floating-point-dictionary-key-overwrite-an-integer-key-with-the-same-v/32211042#32211042)