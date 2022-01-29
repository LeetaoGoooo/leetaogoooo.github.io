---
title: python内置常量(built-in constants)-NotImplemented
tags: [Python,Python标准库]

date: 2017-11-16
published: true
hideInList: false
feature: 
isTop: false
---








# 定义
NotImplemented 的在标准库的定义如下:
>Special value which should be returned by the binary special methods (e.g. *__eq__()* , *__lt__()*, *__add__()*, *__rsub__()*, etc.) to indicate that the operation is not implemented with respect to the other type; may be returned by the in-place binary special methods (e.g. *__imul__()*, *__iand__()*, etc.) for the same purpose. Its truth value is true.

翻译成中文的大概意思:NotImplemented 是一个可由内置函数(*__eq__()* , *__lt__()*, *__add__()*, *__rsub__()* 等等)返回的特殊值,用以表明该操作没有对另一种类型的实现.可能会被其他的特殊方法( *__imul__()*, *__iand__()* 等等)就近处理用以同样的目的,从而返回.它的实际值为True.最后一句话的真是含义如下图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1flj0q0b91oj30nf06z74z.jpg)

与 False,True,None 这几个内置常量不同的是 NotImplemented 对它赋值,并且不会产生 SyntaxError. 如下图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1flj12xyzkvj30nf0b20to.jpg)

## Notimplemented 和 Notimplementederror

在关于 NotImplemented 的定义下面,官方还特意加了一条提示:

>NotImplementedError and NotImplemented are not interchangeable, even though they have similar names and purposes.

## NotImplementedError
官方定义如下:
>This exception is derived from RuntimeError. In user defined base classes, abstract methods should raise this exception when they require derived classes to override the method, or while the class is being developed to indicate that the real implementation still needs to be added.

翻译过来的意思是说:此异常从 RuntimeError 派生出来的.在用户定义的基类中，当抽象方法需要派生类覆盖方法时，或者在开发该类以指示仍然需要添加实际实现时，抽象方法应该引发此异常.

同样有一条提示:
>It should not be used to indicate that an operator or method is not meant to be supported at all – in that case either leave the operator / method undefined or, if a subclass, set it to None.

大概意思是指 NotImplementedError 根本不应该被用来表示一个操作符或者方法不被支持的情况(对于一下 NotImplemented,but why?just see next).在这种情况下，要么保留操作符/方法未定义，要么将子类设置为无.

## Why return NotImplemented instead of raising NotImplementedError

前者是常量后者是异常,但是为什么在操作没有实现的时候,返回前者而不是后者呢?

在 Python 中对列表进行排序时，会经常间接使用类似 *__lt__()* 这类比较运算的方法.有时 Python 的内部算法会选择别的方法来确定比较结果，或者直接选择一个默认的结果。如果抛出一个异常，则会打破排序运算，因此如果使用 NotImplemented 则不会抛出异常，这样 Python 可以尝试别的方法。

NotImplemented 在运行时发出信号，要求其他人满足操作. 在表达式 <code>a==b</code> 中，如果 <code>*a.____eq____(B)*</code>返回 <code>NotImplented</code>，那么 Python 将尝试 <code>*b.____eq____(A)*</code>.如果b知道的足够多，可以返回 <code>true</code> 或 <code>false</code>,那么表达式就可以成功. 如果没有，那么运行时将返回到内置行为(基于 <code>==</code> 和 <code>!=</code>的标识.

# 使用

我们通过实现上面提到的 *__eq__()* 来实现上面提到的例子,来加深印象.

```python
# example.py

class A(object):
    def __init__(self, value):
        self.value = value
 
    def __eq__(self, other):
        if isinstance(other, A):
            print('Comparing an A with an A')
            return other.value == self.value
        if isinstance(other, B):
            print('Comparing an A with a B')
            return other.value == self.value
        print('Could not compare A with the other class')
        return NotImplemented

class B(object):
    def __init__(self, value):
        self.value = value
 
    def __eq__(self, other):
        if isinstance(other, B):
            print('Comparing a B with another B')
            return other.value == self.value
        print('Could not compare B with the other class')
        return NotImplemented
```

然后运行结果如下图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1flj22ol8a7j30nf0cst9j.jpg)

对于 <code>a == a</code> 和 <code>b == b </code>,显然结果如我们预期那样,因为我们实现已经定义好了自己比较自己的方法.重点在 <code> b == a</code>,我们并没有在 B 中定义 B 和 其他类比较的方法,因此打印出了 "Could not compare B with the other class",然后程序接下来去调用了 <code>*a.____eq____(B)*</code>,一切如我们之前所说的那样.


# 参考

[NotImplemented](https://docs.python.org/3.6/library/constants.html#NotImplemented)

[exception NotImplementedError](https://docs.python.org/3.6/library/exceptions.html#NotImplementedError)

[Why return NotImplemented instead of raising NotImplementedError](https://stackoverflow.com/questions/878943/why-return-notimplemented-instead-of-raising-notimplementederror)

[Python中内置的NotImplemented类型](http://python.jobbole.com/80913/)