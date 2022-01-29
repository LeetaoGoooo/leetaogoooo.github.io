---
title: '类的自定注册以及应用场景'
date: 2021-12-25 10:38:21
tags: [python,pep]
published: true
hideInList: false
feature: /post-images/3K1fxgdEp.jpeg
isTop: false
---
> 在 3.6 之前可以通过 `meta class` 去实现，3.6 之后可以通过 `__init_subclass__` 实现


## `meta class`

通过 `meta class` 的 `__new__` 方法可以实现自动注册 `class` 的功能，原理很简单，就是通过元类去控制类的创建，在调用 `__new__`  方法的时候自动将 `class` 注册

```python
class MetaClass(type):
    def __new__(cls, clsname, bases, attrs):
        newclass = super(MetaClass, cls).__new__(cls, clsname, bases, attrs)
        register(newclass)  # 注册函数
        return newclass

class MyClass(metaclass=MetaClass):
    pass
```


## `__init_subclass__` 
`__init_subclass__` 是 3.6 后引入的一个新的特性，一个 hook，可以让所有的子类在创建之后执行一些初始化的操作，通过这个特性我们就可以更简单地实现上述通过元类实现的功能。

```python
class ParentClass:
	def __init_subclass_(cls, **kwargs):
		super().__init_subclass(**kwargs)
		register(cls)


class ChildClass(ParentClass):
	pass

```


需要注意的是 `__init_subclass__`  **没有非关键字参数**。

# 应用
代码存在如下的逻辑:

```python
if category == 'a':
	handle_a()
elif cateogory == 'b':
	handle_b()
elif category == 'c':
	handle_c()
else:
	handle_else()
```

存在很多都处理逻辑，每个逻辑可能由不同的开发人员编写，全部放到一个代码块中，可以预见随着分支的增多，这部分代码会变得越来越庞大，不利于后期维护，所以最简单的方案，存在一个 dict 保存 `category` 和 处理方法的映射关系:

```python
category_to_func = {
    'a': handle_a
	...
}
```

然后就可以将最初的代码简化为:

```python
return category_to_func.get(category)()
```

# 实现

## 方法一
基于上面的思路，可以写出最简单的方法，让大家自行编写自己的处理方法，然后在 `category_to_func` 中统一注册自己的方法。

```python
category_to_func = {
	'a': handle_a # A 撰写注册
	'b': handle_b # B 撰写注册
	...
}
```
这样子没有任何问题，但是不够优雅，开发人员撰写了自己的处理方法之后，还要去指定的地方注册自己的方法，开发体验不太好。
如果存在一种方式，开发人员只需要撰写处理方法，代码可以自定注册就更好了。

## 方法二
通过上面的自注册的方法，让子类继承父类，然后将其自动注册到全局当中，这样子开发人员只需要关注自己的业务实现就可以了。

## 参考链接

[PEP 487 -- Simpler customisation of class creation](https://www.python.org/dev/peps/pep-0487/)
[python - How to auto register a class when it's defined - Stack Overflow](https://stackoverflow.com/questions/5189232/how-to-auto-register-a-class-when-its-defined)