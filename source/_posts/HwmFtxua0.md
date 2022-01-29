---
title: 'pep 544 Protocols: Structural subtyping (static duck typing)'
date: 2021-11-29 08:17:04
tags: [Python]
published: true
hideInList: false
feature: /post-images/HwmFtxua0.jpeg
isTop: false
---
# 前言

在 [PEP 484](https://www.python.org/dev/peps/pep-0484)  中引入的 Typing hints (类型提示) 可以用来为静态类型检查器和其他第三方工具指定类型元数据。但是在，[PEP 484](https://www.python.org/dev/peps/pep-0484)  只指定了名义子类型的语义。在这个 [PEP 544](https://www.python.org/dev/peps/pep-0544/) 中，指定了协议类的静态和运行时语义，这将为结构性子类型（静态鸭子类型）提供一个支持。

## 什么是鸭子类型(Duck Typing)？

> If it walks like a duck and it quacks like a duck, then it must be a duck。
> 

简单地说，”如果它走路像鸭子，叫起来像鸭子，那它一定是鸭子“。

在编程中这就意味着当我们编写接收特定输入的函数时，我们只需要关心该函数输入的行为、属性，而不是该函数输入的显式类型。

> 例如，在不使用鸭子类型的语言中，我们可以编写一个函数，它接受一个类型为"鸭子"的对象，并调用它的"走"和"叫"方法。在使用鸭子类型的语言中，这样的一个函数可以接受一个任意类型的对象，并调用它的"走"和"叫"方法。— 百度百科
> 

那么如何在 Python 中实现“鸭子类型”呢？

# 鸭子类型

现在有这样子场景，我们需要编写一个函数去计算一个拥有边长(len_side)的物体的周长，伪代码如下：

```sql
function calcute_circumference(shape):
		circumference = 0 
		for side in shape.len_side: /// 遍历边长
			circumference += side
		return circumference /// 周长
```

从伪代码不难看出，我们不关心是什么的物体，我们只需要保证这个物体可以有一个 len_side（列表） 字段就可以了。

现在我们可以借由 `Protocol` 去实现它。

```python
from typing import Protocol

class ObjWithSideLen(Protocol):
	side_len: list

def calcute_circumference(shape: ObjWithSideLen):
	circumference = 0 
	for side in shape.side_len:
		circumference += side
	return circumference
```

从上面的例子不难看出 `Protocol` 有点类似 Java 中的接口，只需要在函数上使用它，并不需要去关注输入的参数的具体类型。

# 泛型

我们还可以配合使用 `typing` 中的 `TypeVar` 实现泛型参数化，让我们的函数更加抽象化，只要符合协议，无论什么类型的输入都可以。

```python
from typing import Protocol

class ObjWithSideLen(Protocol):
	side_len: list

T = TypeVar('T', bound=ObjWithSideLen)

def calcute_circumference(shape: T) -> T:
	circumference = 0 
	for side in shape.side_len:
		circumference += side
	shape.circumference = circumference
	return shape
```

# 延伸

上述的例子都可以借由 Python 中的 ABCs 去实现的，但是两者的侧重点有所不同，由于篇幅有限，在下一篇文章，让我们仔细对比一下两者的的区别。

- 参考文章
    
    [python-typing](https://docs.python.org/zh-cn/3/library/typing.html)
    
    [Static Duck Typing in Python with Protocols](https://www.daan.fyi/writings/python-protocols)
    
    [PEP 544](https://www.python.org/dev/peps/pep-0544/)