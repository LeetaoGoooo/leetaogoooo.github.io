---
title:  Python 字符串类型枚举实现
tags: [Python]
date: 2022-07-10
published: true
---

# 前言

Python 的枚举也是一个在时常被被经常使用的一个特性，在 Python3.11 之前，Python 标准库支持枚举类型除了枚举基类 Enum，就只有 IntEnum 了(创建 int 子枚举类型常量的基类)，Python 3.11 开始支持了 StrEnum，如果你的当前使用版本是 Python3.11 则可以略过一下内容。
接下来要介绍的内容，则是在 Python3.11 之前的版本，如何实现 StrEnum 以及一些注意事项。

# StrEnum 的实现

StrEnum 的实现很简单

``` python
class StrEnum(str, Enum):
  pass
```

当子类化 [Enum](https://docs.python.org/zh-cn/3.10/library/enum.html#enum.Enum) 时，在基类序列中的混合类型必须出现于 [Enum](https://docs.python.org/zh-cn/3.10/library/enum.html#enum.Enum) 本身之前,否则会报错

``` python
>>> class StrEnum(Enum,str):
...     pass
...
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/Cellar/python@3.9/3.9.5/Frameworks/Python.framework/Versions/3.9/lib/python3.9/enum.py", line 172, in __prepare__
    member_type, first_enum = metacls._get_mixins_(cls, bases)
  File "/usr/local/Cellar/python@3.9/3.9.5/Frameworks/Python.framework/Versions/3.9/lib/python3.9/enum.py", line 587, in _get_mixins_
    raise TypeError("new enumerations should be created as "
TypeError: new enumerations should be created as `EnumName([mixin_type, ...] [data_type,] enum_type)`
```

使用同样很简单

``` python
>>> class WorkDay(str, Enum):
...   Mon = "Mon"
...   Tue = "Tue"
...   Wed = "Wed"
...   Thu = "Thu"
...   Fri = "Fri"
...
>>> WorkDay.Mon
<WorkDay.Mon: 'Mon'>
>>> WorkDay.Mon == "Mon"
True
```

## StrEnum 和 auto()

使用过 Enum 的肯定对 auto 这个不陌生，它会以合适的值代替 Enum 的实例，默认从 1 开始，在 StrEnum 则是以 "1" 的字符串形式开始

``` python
>>> class AutoStr(str, Enum):
...     a = auto()
...     b = auto()
...
>>> AutoStr.a
<AutoStr.a: '1'>
>>> AutoStr.b
<AutoStr.b: '2'>
```

# auto()

如果希望可以自定义 auto 的起始值的话，你可以这样

``` python
>>> class CustomAuto(Enum):
...     a = 3
...     b = auto()
...     c = auto()
...
>>> CustomAuto.a
<CustomAuto.a: 3>
>>> CustomAuto.b
<CustomAuto.b: 4>
>>> CustomAuto.c
<CustomAuto.c: 5>
```

也可以使用 `_generate_next_value_` 方法去实现

``` python
>>> class A(Enum):
...     def _generate_next_value_(name, start, count, last_values):
...             return count+10
...
>>> class B(A):
...     a = auto()
...     b = auto()
...
>>> B.a
<B.a: 10>
>>> B.b
<B.b: 11>
```

# 参考文档

[python-enum](https://docs.python.org/zh-cn/3.10/library/enum.html)
