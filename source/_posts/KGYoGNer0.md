---
title: 'Dataclasses 、 Attrs 和 Pydantic'
date: 2021-09-13 18:14:35
tags: [python,Python标准库]
published: true
hideInList: false
feature: 
isTop: false
---
# 前言

Python3.7 引入了一个新的模块那就是 `dataclasses`，早在 3.6 版本的时候我就通过安装 `dataclasses` 三方库体验了一波，那么为什么要用 `dataclasses` 呢？

## 为什么使用 dataclasses

一个简单的场景，当你想定义一个对象的属性的时候，比如一本书，通常你会这样

```python
class Book:
    def __init__(self, name: str, price: float, author:str = "佚名"):
        self.name = name
        self.price = price
        self.author = author
```

如果在不定义 `__repr__` 的情况下，初始化这个对象，并输出的话:

```python
>>> book = Book('桃子',10.0,author='桃子')
>>> book
<__main__.Book object at 0x10b69cbb0>
>>> str(book)
'<__main__.Book object at 0x10b69cbb0>'
>>> repr(book)
'<__main__.Book object at 0x10b69cbb0>'
```

显然输出不够友好，对于属性比较少的对象，定义一个 `__repr__` 并不太麻烦，但是一旦需要定义的对象很多或者属性很多的情况下，这样子做就显得略微麻烦了一点。这个时候 `dataclasses` 就派上用场了。

# dataclasses

我们用 `dataclasses` 把 `Book` 改造一下

```python
from dataclasses import dataclass

@dataclass
class Book:
	name:str
	price:float
	author:str = '桃子'
```

然后简单验证一下:

```python
>>> from dataclasses import dataclass,asdict,astuple
>>> @dataclass
... class Book:
...     name:str
...     price:float
...     author:str
...
>>> book1 = Book(name='桃子1',price=10.0,author='桃子1')
>>> book1
Book(name='桃子1', price=10.0, author='桃子1')
>>> asdict(book1)
{'name': '桃子1', 'price': 10.0, 'author': '桃子1'}
>>> astuple(b1)
('桃子1', 12.0, '桃子')
```

`dataclasses` 甚至还具备 `asdict` 函数可以将对象转成 `dict`，也存在 `astuple` 可以将对象转成`tupple` ,是不是很方便,但是还不够，有时候我们对不同对参数进行一定对校验，很遗憾 `dataclasses` 并不能做到，这个时候就需要看 `attrs` 和 `pydantic` 了。

除此之外，`attrs` 和 `pydantic` 还有其他的 `dataclasses` 不具备的特性，见下表：

![](https://www.leetao94.cn/post-images/1631528189775.png)


# attrs vs pydantic

attrs 和 pydantic 都需要通过 pip 安装

```bash
pip install attrs
pip install pydantic
```

## validator

### attrs 中的 validators

```bash
import attr

@attr.s
class Book:
    name: str = attr.ib(default=None)
    price:float = attr.ib(default=None,validator=attr.validators.instance_of(float))
    author: str = attr.ib(default='桃子')

    @price.validator
    def more_than_zero(self, attribute, value):
        if not value > 0:
            raise ValueError("价格不能少于 0 元!")

book = Book(name='attrs',price=-1.0,author='hynek Hynek Schlawack')
```

运行一下，验证一下结果：

```bash
Traceback (most recent call last):
  File "test_attrs.py", line 14, in <module>
    book = Book(name='attrs',price=-1.0,author='hynek Hynek Schlawack')
  File "<attrs generated init __main__.Book>", line 6, in __init__
  File "D:\workspace\python\test\venv\lib\site-packages\attr\_make.py", line 2975, in __call__
    v(inst, attr, value)
  File "test_attrs.py", line 12, in more_than_zero
    raise ValueError("价格不能少于 0 元!")
ValueError: 价格不能少于 0 元!
```

### pydantic 中的 validators

```python
from pydantic import BaseModel,validator, ValidationError

class Book(BaseModel):
    name:str
    price:float
    author:str = '桃子'

    @validator("price")
    def more_than_zero(cls, v):
        assert isinstance(v,float)
        if v <= 0:
            raise ValueError("价格不能少于 0 元!")
        return v

book = Book(name='attrs',price=-1.0,author='hynek Hynek Schlawack')
```

同样的验证一下结果:

```python
Traceback (most recent call last):
  File "test_pydantic.py", line 15, in <module>
    book = Book(name='attrs',price=-1.0,author='hynek Hynek Schlawack')
  File "pydantic\main.py", line 406, in pydantic.main.BaseModel.__init__
pydantic.error_wrappers.ValidationError: 1 validation error for Book
price
  价格不能少于 0 元! (type=value_error)
```

除了 validators ，attrs 和 pydantic 还同时具有  converters（转换器） 功能。

## converters

转换器也是一个十分实用的功能，所谓的转换器就是在将参数在传递到 `__init__`  之前，将它按照需求转换成所需格式的数据。

最常见的就是将 str 转成 datetime 格式，attrs 和 pydantic 在转换器的实现上有所区别：

### attrs 中 的转换器

```python
import attr
from datetime import datetime

def conver_to_datetime(user_input) ->  datetime:
    if isinstance(user_input,str):
        return datetime.strptime(user_input, '%Y-%m-%d %H:%M:%S')
    elif isinstance(user_input,datetime):
        return user_input
    raise TypeError("不支持的参数类型，仅支持:str 和 datetime")

@attr.s
class Book:
    # ... 省略
    publish_date: datetime = attr.ib(default=None, converter=conver_to_datetime)

book = Book(name='attrs',price=1.0,author='hynek Hynek Schlawack',publish_date='2021-09-13 00:00:00')
print(book)
```

运行程序

```python
Book(name='attrs', price=1.0, author='hynek Hynek Schlawack', publish_date=datetime.datetime(2021, 9, 13, 0, 0))
```

不难看出，str 类型的字符串被成功转化了。

### pydantic 中的转换器

```python
from pydantic import BaseModel,validator
from datetime import datetime

class Book(BaseModel):
		# 省略...
    publish_date: datetime

    @validator("publish_date")
    def conver_to_datetime(cls, user_input):
        if isinstance(user_input,str):
            return datetime.strptime(user_input, '%Y-%m-%d %H:%M:%S')
        elif isinstance(user_input,datetime):
            return user_input
        raise TypeError("不支持的参数类型，仅支持:str 和 datetime")

book = Book(name='attrs',price=1.0,author='hynek Hynek Schlawack', publish_date='2021-09-13 16:03:00')
print(book)
```

验证结果:

```python
name='attrs' price=1.0 author='hynek Hynek Schlawack' publish_date=datetime.datetime(2021, 9, 13, 16, 3)
```

从上面的代码不难看出，attrs 本身内置 converter 参数，可以通过传递内置函数或者自定义函数实现转化器的功能，而 pydantic 则需要借助 validator 去实现，在这一点上 pydantic 还是稍微逊色 attrs。

除了上面提到的两个功能，attrs 和 pydantic 都具备 `immutable` 的方法用来修饰属性，从而实现属性不可修改。

attrs 还具备 `slots` 和 `programmatic creation` 有兴趣的可以进一步阅读官方文档。

# 总结

从上面的例子，不难看出 pydantic 有下面几个问题：

1. pydantic 不支持位置参数
2. pydantic 的输出有点奇怪，没有带上类名
3. pydantic 不支持 `slots` 和 `programmatic creation`
4. [pydantic 不支持 Collection 类型](https://github.com/samuelcolvin/pydantic/issues/2605) 

在参考文章中还提到了 pydantic 对 unions 的策略有问题，不容易定制，并且对定制的（非）结构化的支持很弱。

所以如果有复杂的需求的话，建议使用 `attrs` ，只是想简单的呈现对象的属性的话，可以考虑用 `dataclasses` 。

# 参考文章

[dataclasses vs attrs vs Pydantic](https://jackmckew.dev/dataclasses-vs-attrs-vs-pydantic.html)

[attrs 和 Python3.7 的 dataclasses](https://zhuanlan.zhihu.com/p/34963159)

[Why I use attrs instead of pydantic](https://threeofwands.com/why-i-use-attrs-instead-of-pydantic/amp/?__twitter_impression=true)