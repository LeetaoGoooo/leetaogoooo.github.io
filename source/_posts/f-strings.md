---
title: f-strings
tags: [Python,Python 基础]

date: 2019-05-22
published: true
hideInList: false
feature: 
isTop: false
---





# 前言

在 Python 3.6 之后,除了原有的 % 和 str.format() 字符串格式化的方法, 又新增了一种 f-strings. 官方也更加推荐使用 f-strings. 那么 f-strings 有哪些喜人的特性呢?

# 特性

## 支持 Python 表达式

### 运算表达式

```
>>> num=1
>>> f'add={num+1}'
'add=2'
```

### 函数

```
>>> a=[1,2,3]
>>> f'len:{len(a)}'
'len:3'
```
除了调用内置函数,自然也是支持自定义函数

### 类

```
>>> class Test:
...     def __str__(self):
...             return "Hello I'm __str__"
...     def __repr__(self):
...             return "Hello I'm __repr__"
...
>>> test = Test()
>>> f'{test}'
"Hello I'm __str__"
>>> f'{repr(test)}'
"Hello I'm __repr__"
>>> test
Hello I'm __repr__
```

## 支持 lambdas 表达式

```
>>> f'{(lambda x: x*2)(3)}'
'6'
```

## 支持 Triple quotes

```
 f'''{test}'''
"Hello I'm __str__"
```

## 支持 Raw 字符串

```

>>> header = 'Subject'
>>> fr'{header}:\s+'
'Subject:\\s+'
```

**注意**: 虽然支持 fr'' 但是并不支持 fb'' 和 fu''

说完了特性,那么接下来就需要说一说 f-strings 和 str.format() 的区别了

# f-strings 和 str.format() 的区别

## 全表达式和有限制表达式

str.format() 和 f-strings 在一定程度上都支持表达式，但是前者有一定的局限性

### 例子

```
>>> points = 19
>>> total = 22
>>> 'Correct answers: {:.2%}'.format(points/total)
'Correct answers: 86.36%'
>>> "repr() shows quotes: {!r}; str() doesn't: {!s}".format('test1', 'test2')
"repr() shows quotes: 'test1'; str() doesn't: test2"
```

## 索引的查找

在 str.format() 中，对于索引值看起来不像数字的都会被转换为字符串

```
>>> d = {'a': 10, 'b': 20}
>>> 'a={d[a]}'.format(d=d)
'a=10'
```

**注意**:索引值直接被转换成字符串 'a' 了，但是在 f-strings, 你需要使用 “a”

```
>>> f'a={d["a"]}'
'a=10'
```

是不是觉得很多余？实际上并不是，如果不这样子要求的话，你是没有办法使用变量作为索引的

```
>>> a = 'b'
>>> f'a={d[a]}'
'a=20'
```


# 最后

自 3.6 之后，官方推荐使用 f-strings,相较于上面的说提到的特性，f-strings 的可读性也比 str.format() 更高，尤其当你的字符串比较长的时候更加明显，所以如果你在使用 3.6 之后的版本，可以尝试这从 str.format() 投入 f-strings 的怀抱了