---
title: python 中实现类方法重载
date: 2022-02-14
tags: [python, singledispatch]
categories:
comments: true
---

# 前言

在 Java 中有 [[重载]] 的概念：

> 重载(overloading) 是在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。每个重载的方法（或者构造函数）都必须有一个独一无二的参数类型列表。

Python 本身不支持 `重载` 这个特性，但是通过 `functools.singledispatch`  可以实现函数的重载。接下来通过一个例子，简单地演示一下 Python 的函数重载。

<!--more-->

```python
from functools import singledispatch

@singledispatch
def fun(arg):
    print(arg)


@fun.register
def _(arg: int):
    print(f'arg is int: {arg}')


@fun.register
def _(arg: list):
    print(f'arg is list: {arg}')

@fun.register
def _(arg: str):
    print(f'arg is str: {arg}')


if __name__ == '__main__':
    fun([1,2,3])
    fun(1)
    fun('str')
```

运行程序查看一下结果
```shell
arg is list: [1, 2, 3]
arg is int: 1
arg is str: str
```
根据参数的不同的类型，成功了执行了对应的函数，到这里我们完成了 Python 中对函数的`重载`。Java 中 `重载` 不仅仅针对普通函数，其类方法也支持这个特性，那么 Python 如何实现类方法的`重载`呢？

# 类方法重载
## Python >= 3.8
当 Python 版本不低于 3.8 的时候，`functools` 新增了一个 `singledispatchmethod` 方法，这个方法可以让 Python 的类方法支持 `重载`，使用方法和 `singledispatch` 类似，唯一需要注意的是，重载的类型是由类函数中第一个非 `self` 和 `cls` 的参数类型决定的。

看一下官方实例：
```python
class Negator:
    @singledispatchmethod
    def neg(self, arg):
        raise NotImplementedError("Cannot negate a")

    @neg.register
    def _(self, arg: int):
        return -arg

    @neg.register
    def _(self, arg: bool):
        return not arg
```

除此之外，`singledispatchmethod` 还支持嵌套的装饰器，不过需要注意的是，`dispatcher.register` 和 `singledispatchmethod` 需要在绝大多数装饰器的最上层，基于上面的例子修改一下

```python
class Negator:
    @singledispatchmethod
    @classmethod
    def neg(cls, arg):
        raise NotImplementedError("Cannot negate a")

    @neg.register
    @classmethod
    def _(cls, arg: int):
        return -arg

    @neg.register
    @classmethod
    def _(cls, arg: bool):
        return not arg
```

## Python < 3.8
当 Python 版本小于 3.8 的时候，由于 Python 本身不支持类方法的`重载`，则需要我们自行实现。在实现之前，可以先借鉴一下 [`singledispatch` 的源码]([cpython: f6f691ff27b9 Lib/functools.py](https://hg.python.org/cpython/file/f6f691ff27b9/Lib/functools.py#l706))

```python
    def wrapper(*args, **kw):
        return dispatch(args[0].__class__)(*args, **kw)
```

从源码中不难看得出 `singledispatch` 最终返回的就是上述的 `wrapper` 函数，并且函数是根据函数的第一个参数的类型（`args[0].__class__`）最终来实现函数的`重载`的，对于函数这种实现方式没有任何问题，但是如果是类方法的话，第一个参数始终是 `self`，则无法进行区分了，`重载`就更无从谈起了。

所以实现类方法的重载难点在于重写 `wrapper` 函数，所以在 `singledispatch` 基础上我们定义一个函数对`wrapper` 进行重写，把 wrapper 中的参数 `args[0]` 调整为 `args[1]`

```python
from functools import singledispatch, update_wrapper

def methdispatch(func):
    dispatcher = singledispatch(func)
    def wrapper(*args, **kw):
        return dispatcher.dispatch(args[1].__class__)(*args, **kw)
    wrapper.register = dispatcher.register
    update_wrapper(wrapper, func)
    return wrapper
```

接下来测试一下这个方法：
```python
class Negator:
    @methdispatch
    def neg(self, arg):
        raise NotImplementedError("Cannot negate a")

    @neg.register
    def _(self, arg: int):
        return -arg

    @neg.register
    def _(self, arg: bool):
        return not arg

if __name__ == '__main__':
    neg = Negator()
    neg.neg(1)
    neg.neg(False)
```

运行程序：
```shell
-1
True
```
到此手动实现类方法的重载成功。

# 其他

在 pypi 上也有对应的 [singledispatchmethod · PyPI](https://pypi.org/project/singledispatchmethod/) 库，可以通过 pip 安装后直接使用，用法和 3.8 之后版本用法一致。



# 参考

1. [python - How can I use functools.singledispatch with instance methods? - Stack Overflow](https://stackoverflow.com/questions/24601722/how-can-i-use-functools-singledispatch-with-instance-methods?utm_source=pocket_mylist)
2. [cpython: f6f691ff27b9 Lib/functools.py](https://hg.python.org/cpython/file/f6f691ff27b9/Lib/functools.py#l706)
3. [PEP 3124 -- Overloading, Generic Functions, Interfaces, and Adaptation (python.org)](https://legacy.python.org/dev/peps/pep-3124/)

