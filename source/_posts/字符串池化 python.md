---
title: 字符串池化 python
tags: [Python]

date: 2018-12-24
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

在 Python 中经常通过内存池化技术来提高其性能,那么问题来了,在什么情况下会池化呢? 让我们通过几个例子进行一下理解一下.

# 预备知识

在查看例子之前,首先要提 python 中的一个函数 **id()**,让我们看一下函数说明:

```bash
id(obj, /)
    Return the identity of an object.

    This is guaranteed to be unique among simultaneously existing objects.
    (CPython uses the object \'s memory address.)
```

通过上述说明,可以得知 id() 将会返回对像的唯一标识,在 CPython 中将会返回内存地址,也就是说如果两个对象 的 id 值一样,可以说着两个对象是相同的了.

# 例子

example 00

```python
a = ""
b = ""
print(id(a),id(b))
print(a is b)
```

输出结果:

```bash
>>> a = ""
>>> b = ""
>>> print(id(a),id(b))
2114853370544 2114853370544
>>> print(a is b)
True
```

example 01

```python
a = "a"
b = "a"
print(id(a),id(b))
print(a is b)
```

输出结果:

```bash
>>> a = "a"
>>> b = "a"
>>> print(id(a),id(b))
2114883022608 2114883022608
>>> print(a is b)
True
```

example 02

```python
a = "magic_string"
b = "magic" + "_" + "string"
print(id(a),id(b))
print(a is b)
```

输出结果:

```bash
>>> a = "magic_string"
>>> b = "magic" + "_" + "string"
>>> print(id(a),id(b))
2114887161136 2114887161136
>>> print(a is b)
True
```

example 03

```python
a = "magic!"
b = "mgaic!"
print(id(a),id(b))
print(a is b)
```

输出结果:

```bash
>>> a = "magic!"
>>> b = "mgaic!"
>>> print(id(a),id(b))
2114885855416 2114889455408
>>> print(a is b)
False
```

example 04

```python
a,b = "magic!","magic!"
print(id(a),id(b))
print(a is b)
```

输出结果:

```bash
>>> a,b = "magic!","magic!"
>>> print(id(a),id(b))
2114885691912 2114885691912
>>> print(a is b)
True
```

example 05

```python
a = "!"
b = "!"
print(id(a),id(b))
print(a is b)
```

输出结果：

```bash
>>> a = "!"
>>> b = "!"
>>> print(id(a),id(b))
140564571922024 140564571922024
>>> print(a is b)
True

```

example 06

```python
print(a*20 is 'aaaaaaaaaaaaaaaaaaaa')
print(a*21 is 'aaaaaaaaaaaaaaaaaaaaa')
```

输出结果:

```bash
>>> print(a*20 is 'aaaaaaaaaaaaaaaaaaaa')
False
>>> print(a*21 is 'aaaaaaaaaaaaaaaaaaaaa')
False
```

# 总结

通过上述 7 个例子,我们不难对 python 的字符串池化有个大概的认识,我们这里做个简单的总结:

1. 通过 example 00,01,05,我们可以得出对于长度为 0 或者 1 的字符串会被池化
2. 通过 example 02,03,我们可以得出字符串中只包含字母数字以及下划线的字符串会被池化
3. 通过 example 04, 我们可以得出当在同一行对不同变量，赋值如果相同的话，它们将会指向同一个对象，注意这里面的 “magic!” 并不符合池化的要求，这只是一种编译器的优化
4. example 06 所出现的现象在 python 中有一个专业的术语，讲常量折叠(constant folding),顾名思义，在编译优化时，讲能够计算出的结果的变量直接替换为常量.但是这没有限制吗？显然不是的，在我们的例子中已经发现，当长度超过20的时候，折叠就会失效了，试想一下，如果没有限制的话，初始化的字符串过长，将会严重导致性能的下降以及内存的消耗


# 参考链接

1. [The internals of Python string interning](http://guilload.com/python-string-interning/)

2. [exploring python code objects](https://late.am/post/2012/03/26/exploring-python-code-objects.html)

3. [Python string interning](https://stackoverflow.com/questions/15541404/python-string-interning)

4. [Python String objects implementation](http://www.laurentluce.com/posts/python-string-objects-implementation/)