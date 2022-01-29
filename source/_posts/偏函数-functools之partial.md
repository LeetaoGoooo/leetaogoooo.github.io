---
title: 偏函数-functools之partial
tags: [Python]

date: 2017-11-06
published: true
hideInList: false
feature: 
isTop: false
---








前段时间在讲述 Flask-Login 的源码中遇见了一个函数 ——<code>partial</code>,这个函数来自于<code>functools</code>模块,这个模块定义为"The functools module is for higher-order functions: functions that act on or return other functions. In general, any callable object can be treated as a function for the purposes of this module",翻译成中文的大概意思就是"functools 用于高阶函数：指那些作用于函数或者返回其他函数的函数。总的来说，这个模块的目标就是将任何可调用的对象都可以被视为函数。"

# functools.partial

返回一个新的部分对象,当被调用的时候,相当于原有的对象携带了默认的参数.如果在调用过程中传递了更多的参数,将会被追加到args.如果提供了其他的关键字参数,则会覆盖或者扩展关键字:

```python
def partial(func, *args, **keywords):
    def newfunc(*fargs, **fkeywords):
        newkeywords = keywords.copy()
        newkeywords.update(fkeywords)
        return func(*(args + fargs), **newkeywords)
    newfunc.func = func
    newfunc.args = args
    newfunc.keywords = keywords
    return newfunc
```

当函数的参数个数太多，需要简化时，使用functools.partial可以创建一个新的函数，这个新函数可以固定住原函数的部分参数，从而在调用时更简单.

 eg:
 
 <code>int()</code>函数,可以将字符串转为整数,简单看一下它的函数使用说明:
 
```python
 class int(object)
 |  int(x=0) -> integer
 |  int(x, base=10) -> integer
 |
 |  Convert a number or string to an integer, or return 0 if no arguments
 |  are given.  If x is a number, return x.__int__().  For floating point
 |  numbers, this truncates towards zero.
 |
 |  If x is not a number or if base is given, then x must be a string,
 |  bytes, or bytearray instance representing an integer literal in the
 |  given base.  The literal can be preceded by '+' or '-' and be surrounded
 |  by whitespace.  The base defaults to 10.  Valid bases are 0 and 2-36.
 |  Base 0 means to interpret the base from the string as an integer literal.
 |  >>> int('0b100', base=0)
 |  4
```
从上述说明中我们可以看见,int还有一个额外的参数<code>base</code>,base 默认值为10,也就意味着默认情况下将字符串转化为十进制的数.如果我们需要转化成二进制怎么办?在不使用其他模块的情况下,只需要<code>int(x,base=2)</code>,那么如果是借助<code>partial</code>,该如何使用呢?

```python
>>>from functools import partial
>>>int2 = partial(int,base=2) # 这里是传递了关键字参数,即keywords
>>>int2('1000')
>>>8
>>>str_3_int = partial(int,'3') # 传递了参数args
>>>str_3_int()
>>>3 # 不够清楚的话可以接着看下面的例子
>>>max_with_10 = partial(max,10)
>>>max_with_10(2,3,4)
>>>10
```

