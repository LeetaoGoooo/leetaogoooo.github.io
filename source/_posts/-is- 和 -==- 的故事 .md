---
title: -is- 和 -==- 的故事 
tags: [Python]

date: 2019-01-04
published: true
hideInList: false
feature: 
isTop: false
---


# 前言

相比写过 Python 的小伙伴对 "is" 和 "==" 这两个运算符肯定不会陌生,但是你真的能清楚两者的区别吗? 别着急,听我慢慢道来.

# is 和 ==

"is" 和 "==" 都是用来比较两个对象的,区别在于 "is" 是用来判断,两个对象是否为相同,即它们实际的内存地址是否相等,也就是说 **id()** 值是否一样, 而 "==" 就很简单了,它只是比较两个对象的值是不是相同.

是不是很简单,让我们结合之前的知识点进行简单的测试.

# 例子

1.

```python
>>> a = "leetao"
>>> b = "leetao"
>>> a == b # 
>>>> a is b # 
```
第一组输出分别是什么呢?让我们揭晓答案:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fyukc0x53ij30b00560sl.jpg)

这个例子如果错了,需要看一下我之前写的关于字符串池化的那篇文章了,这里字符串 "leetao" 被池化了,所以 a,b 两者实际指向同一块内存.

2.

```python
>>> a = b = ["l","e","e","t","a","o"]
>>> c = ["l","e","e","t","a","o"]
>>> a is b # 
>>> a == b # 
>>> a is c # 
>>> a == c # 
```

第二组的正确答案:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fyukdqu3qjj30gf08njrd.jpg)

这个例子也不能理解,之前关于字符串池化的时候,也提过类似的例子,a,b 在同一行赋值, python 解释器进行了优化,将两者指向同一个对象,所以导致 a 和 b 值相等, id 也相等, 至于 a 和 c 很好理解,它们值相等,但是很明显,id值不一样.

3

```python
>>> a = 256
>>> b = 256
>>> a is b # 
>>> a == b #

>>> c = 257
>>> d = 257
>>> c is d #
>>> c == d #
```

第三组就更有意思了,答案:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fyukezmmo8j307n0a4q2w.jpg)

这个例子,牵扯到另外一个知识点,关于 Integer 对象的一个小的知识点,让我们看一下官方原文:

> The current implementation keeps an array of integer objects for all integers between -5 and 256, when you create an int in that range you actually just get back a reference to the existing object. So it should be possible to change the value of 1. I suspect the behaviour of Python in this case is undefined. :-)

翻译过来的意思大致就是: 当前实现为-5到256之间的所有整数保留一个整数对象数组，当您在该范围内创建一个int时，实际上只返回对现有对象的引用。 因此应该可以更改值1.我怀疑在这种情况下Python的行为是未定义的。:-)

有了这个知识作为前提,就不难理解,上面的例子的答案了.

# 参考链接

[Integer Objects](https://docs.python.org/3/c-api/long.html#c.PyLong_FromLong)
