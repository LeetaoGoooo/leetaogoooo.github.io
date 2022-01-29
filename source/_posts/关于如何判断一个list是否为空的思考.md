---
title: 关于如何判断一个list是否为空的思考
tags: [Python,Python 基础]

date: 2019-08-21
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

今天随手翻 stackoverflow，看到问题叫 [How do I check if a list is empty?](https://stackoverflow.com/questions/53513/how-do-i-check-if-a-list-is-empty) 一看这个问题，不难猜到到这是一个刚学 Python 的人提问的，因为这个问题实在是太基础了，那么如何判断呢？


# 写法

## 写法一

```python
a = []
if len(a) == 0:
    print("empty list")    
```

## 写法二

```python
a = []
if not a:
    print("empty list")
```

这两种写法都很常见。那么问题来了，第一种写法用列表长度去判断列表为空可以理解，那么第二种写法直接去判断，这是怎么判断出来的呢？

# if

为了解决这个问题，我们首先需要重新认识一下 **if**。关于 if 是用来做条件执行的这一点，大家肯定都知道。 

这里我们顺便看一下官方文档的描述

>The if statement is used for conditional execution:
```
if_stmt ::=  "if" expression ":" suite
             ("elif" expression ":" suite)*
             ["else" ":" suite]
```
>It selects exactly one of the suites by evaluating the expressions one by one until one is found to be **true**; then that suite is executed

注意上面加粗的地方 `true`, 很明显 if 后面表达式预期值应该为 `true` 或者 `false`,也就是说写法二中的 `a` 最后的值也应该为 `true` 或者 `false`,那是怎么做到的呢？

# bool
最简单的办法去判断 `a` 是否为 `true` 或者 `false`，就是使用内置函数 `bool()`

```python
>>> a = []
>>> bool(a)
False
```

正常调用 `bool()` 这个函数的时候，会去调用变量的内建方法 `__bool__()`,那如何看一个变量有哪些内建方法呢？在上一篇[`__name__是什么`]()提到了一个的函数 `dir()`，在这里也同样可以用来使用

```
>>> dir(a)
['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

似乎没有看到 `__bool__()` 这个函数，那怎么办呢？ 不用着急，官方文档中还说了这样的一句话

>When this method (`__bool__()`) is not defined, `__len__()` is called, if it is defined, and the object is considered true if its result is nonzero. If a class defines neither `__len__()` nor `__bool__()`, all its instances are considered true.

当一个变量没有定义 `__bool__` 的时候，不用着急，如果定义了 `__len()__` 这个函数也是可以的，当长度不为 0 的时候则为 `true`。


# 总结

之所以在写法二中可以用 `if` 直接判断列表 `a` 是否为空，是因为对于 `list` 来说，它没有内建方法 `__bool__()`,而有内建方法 `__len__()`，最后通过判断 `list` 长度是否为 0 来得出 `true` 或者 `false`  的，一旦为空，则判断结果为 `false`
