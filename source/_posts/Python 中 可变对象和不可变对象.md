---
title: Python 中 可变对象和不可变对象
tags: [Python,Python 基础]

date: 2019-05-29
published: true
hideInList: false
feature: 
isTop: false
---





在 Python 中一切都可以看作为对象。每个对象都有各自的 id, type 和 value。

1.  id: 当一个对象被创建后，它的 id 就不会在改变，这里的 id 其实就是对象在内存中的地址，可以使用 `id()` 去查看对象在内存中地址。
2.  type: 和 id 一样当对象呗创建之后，它的 type 也不能再被改变，type 决定了该对象所能够支持的操作
3.  value: 对象的值

一个对象可变与否就在于 value 值是否支持改变。

# 不可变对象

常见的不可变对象(immutable objects):

1.    Number: int, float, complex
2.    string
3.    tuple
4.    frozenset:set 的另外一种类型

让我们通过例子感受一下

## 例子

```
>>> x=1
>>> y=x
>>> id(x)==id(y)
True
>>> id(1)==id(y)
True
```

这里创建了两个 `int` 对象，并且让 `x`,`y` 指向同一个对象，然后我们试着修改一下 `x`,让 `x+1`

```
>>>x=x+1
```

这个时候 `x` 的 `id` 值和 `y` 的 `id` 以及 1 的 `id` 还一样吗？

```
>>> x=x+1
>>> id(x)==id(y)
False
>>> id(x)==id(1)
False
>>> id(1)==id(y)
True
```

从结果不难看出来，`x` 所指向的对象已经改变了，但是 `1` 并没有改变。这就是不可变对象，对于不可变对象来说，一旦创建之后就不允许被改变。

**注意**: 不可变并不严格等同绝对的不可变

```
>>> a=([1],2,3)
>>> id(a)
4338862696
>>> id(a[0])
4338949128
>>> a[0][0]=0
>>> id(a)
4338862696
>>> id(a[0])
4338949128
```

从上面的例子不难看出，我们改变了 `a[0]` 的值，但是并没有影响它的 `id` 值。一个不可变容器对象如果包含对可变对象的引用，当后者的值改变时，前者的值也会改变；但是该容器仍属于不可变对象，因为它的 `id` 值不会改变的。

# 可变对象

常见的可变对象(mutable objects):

1.  list
2.  dict
3.  set

同样的通过例子感受一下

## 例子

```
>>> c = [1,2,3]
>>> b = c
>>> id(b)==id(c)
True
```

创建两个 `list` 对象，并且让 `c`,`d` 指向同一个对象，然后我们试着修改一下 `c`

```
c[0]=4
```

这个时候在看一下两个对象的 `id` 值是否一致

```
>>> id(c)==id(b)
True
```

修改之后，`c`,`d` 仍然指向同一个对象，所以不难得出结论,对于一个可变对象来说，修改 `value` 值并不会对它的 `id` 造成任何影响


# 函数参数

对于可变对象和不可变对象，他们两者在函数当作参数传递的时候表现也有所不同

## 例子

### eg0
```python
def update_list(nums_list):
    print(f'2 id:{id(nums_list)}')
    nums_list += [10]
    print(f'3 id:{id(nums_list)}')

    
number_list = [5, 6]
print(f'1:{number_list}, id:{id(number_list)}')      
update_list(number_list)
print(f'4:{number_list}, id:{id(number_list)}') 
```

看一下结果

```
1:[5, 6], id:4457707912
2 id:4457707912
3 id:4457707912
4:[5, 6, 10], id:4457707912
```

在看另外一个例子

### eg1
```python
def add_num(number):
    print(f'2:{id(number)}')
    number += 1
    print(f'3:{id(number)}')


num = 1
print(f'1:{num}, id:{id(num)}')      
add_num(num)
print(f'4:{num}, id:{id(num)}')      
```

看一下结果

```
1:1, id:4343768416
2 id:4343768416
3 id:4343768448
4:1, id:4343768416
```

有其他有些文章中，上面 eg0 被称为引用传递，eg1 被称为值传递。然而在官方文档中，并没有找到有关的这两个名词的任何内容，或者这种方式并不是上述的两者任意一种。

这里的重点是：**对于可变对象，在函数中的值的改变，会导致对象改变；对于不可变对象，在函数中值的改变，对其不会造成任何影响**

# 参考

[Does Python pass by value, or pass by reference?](http://stupidpythonideas.blogspot.com/2013/11/does-python-pass-by-value-or-by.html)

[Is Python call-by-value or call-by-reference? Neither.](https://jeffknupp.com/blog/2012/11/13/is-python-callbyvalue-or-callbyreference-neither/)
