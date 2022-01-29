---
title: 'Python 描述符'
date: 2021-01-01 20:40:56
tags: [Python,Python 基础]
published: true
hideInList: false
feature: 
isTop: false
---
> 学会描述符之后，不仅有更多的工具集可用，还会对 Python 的运作
方式有更深入的理解，并由衷赞叹 Python 设计的优雅。——Raymond Hettinger
Python 核心开发者和专家

# 什么是描述符

描述符 (Descriptor) 是 Python 中一个非常重要的特性，在实际应用中我们经常使用到它，但是也最容易被忽略，property、classmethod、staticmethod。那么究竟什么叫描述符呢？看一下官方的定义：

>In general, a descriptor is an attribute value that has one of the methods in the descriptor protocol. Those methods are `__get__()`,` __set__()`, and `__delete__()`. If any of those methods are defined for an attribute, it is said to be a descriptor.

一般而言，描述器是一个包含了描述器协议中的方法的属性值。 这些方法有 `__get__()`, `__set__() ` 和 `__delete__()`。 如果为某个属性定义了这些方法中的任意一个，它就可以被称为 **descriptor**。

# 如何使用描述符

除了上面提到的三个内置属性，其实在不少 Python 库中都有关于描述符的应用，比如各种 ORM。

## ORM 示例
这里以官方的 ORM 示例做个简单的演示 -- 通过描述符来实现简单的 object relational mapping 框架。
其核心思路是将数据存储在外部数据库中，Python 实例仅持有数据库表中对应的的键。描述器负责对值进行查找或更新：

```python
class Field:

    def __set_name__(self, owner, name):
        self.fetch = f'SELECT {name} FROM {owner.table} WHERE {owner.key}=?;'
        self.store = f'UPDATE {owner.table} SET {name}=? WHERE {owner.key}=?;'

    def __get__(self, obj, objtype=None):
        return conn.execute(self.fetch, [obj.key]).fetchone()[0]

    def __set__(self, obj, value):
        conn.execute(self.store, [value, obj.key])
        conn.commit()
```
可以用 Field 类来定义描述了数据库中每张表的模式的 models

```python
class Movie:
    table = 'Movies'                    # Table name
    key = 'title'                       # Primary key
    director = Field()
    year = Field()

    def __init__(self, key):
        self.key = key

class Song:
    table = 'Music'
    key = 'title'
    artist = Field()
    year = Field()
    genre = Field()

    def __init__(self, key):
        self.key = key
```

然后连接数据库验证一下结果:

```bash
>>> import sqlite3
>>> conn = sqlite3.connect('entertainment.db')
>>> Movie('Star Wars').director
'George Lucas'
>>> jaws = Movie('Jaws')
>>> f'Released in {jaws.year} by {jaws.director}'
'Released in 1975 by Steven Spielberg'

>>> Song('Country Roads').artist
'John Denver'

>>> Movie('Star Wars').director = 'J.J. Abrams'
>>> Movie('Star Wars').director
'J.J. Abrams
```

## 自定义验证器

官方还提供了一个验证器的例子，同样值得一看。验证器是一个用于托管属性访问的描述器。在存储任何数据之前，它会验证新值是否满足各种类型和范围限制。如果不满足这些限制，它将引发异常，从源头上防止数据损坏。

```python
from abc import ABC, abstractmethod

class Validator(ABC):

    def __set_name__(self, owner, name):
        self.private_name = '_' + name

    def __get__(self, obj, objtype=None):
        return getattr(obj, self.private_name)

    def __set__(self, obj, value):
        self.validate(value)
        setattr(obj, self.private_name, value)

    @abstractmethod
    def validate(self, value):
        pass
```
自定义验证器需要从 Validator 继承，并且必须提供 validate() 方法以根据需要测试各种约束。

```python
class OneOf(Validator):

    def __init__(self, *options):
        self.options = set(options)

    def validate(self, value):
        if value not in self.options:
            raise ValueError(f'Expected {value!r} to be one of {self.options!r}')
```

验证结果

```python
class Component:
    kind = OneOf('wood', 'metal', 'plastic')
    #...
    def __init__(self, name, kind, quantity):
        self.kind = kind
        #...
```
描述器会阻止无效实例的创建
```bash
>>> Component('WIDGET', 'metle', 5)      # Blocked: 'metle' is misspelled
Traceback (most recent call last):
    ...
ValueError: Expected 'metle' to be one of {'metal', 'plastic', 'wood'}
```

# 参考

[描述器使用指南](https://docs.python.org/zh-cn/3/howto/descriptor.html#id2)
