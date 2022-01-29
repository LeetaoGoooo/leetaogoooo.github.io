---
title: __str__ 和 __repr__ 的那些事
tags: [Python]

date: 2018-08-23
published: true
hideInList: false
feature: 
isTop: false
---







> talk is cheap,show me the code

# 示例说明

>预备知识:**str（）内置函数使用 <code>__str__</code> 显示对象的字符串表示形式，而 repr（）内置函数使用 <code>__repr__</code> 显示对象**

## 使用缺省 <code>__str__</code> 和 <code>__repr__</code>

```python
class Test:
	pass

print(str(Test()))
print(repr(Test()))
print(str(Test()) == repr(Test()))
```

输出结果:

```
>>> print(str(Test))
<class '__main__.Test'>
>>> print(repr(Test))
<class '__main__.Test'>
>>> print(repr(Test) == str(Test))
True
```

通过上述例子我们可以知道: **python** 默认有对 <code>__repr__</code> 和 <code>__str__</code> 的实现方法,当两者均使用缺省定义的情况下 **<code>__repr__</code> 是等同于 <code>__str__</code>**

## 两者只定义一种 

### 定义 \__str__

```python
class Test1:
	def __str__(self):
    	return "Test1.__str__"

print(str(Test1()))
print(repr(Test1()))
print(str(Test1()) == repr(Test1()))
```

输出结果如下:

```
>>> print(str(Test1()))
Test1.__str__
>>> print(repr(Test1()))
<__main__.Test1 object at 0x000001F9B3DCD518>
>>> print(str(Test1()) == repr(Test1()))
False
```

这里别急，让我们看下另外的例子

### 定义 \__repr__

```python
class Test2:
	def __repr__(self):
    	return "Test1.__str__"

print(str(Test2()))
print(repr(Test2()))
print(str(Test2()) == repr(Test2()))
```

输出结果如下:

```
>>> print(str(Test2()))
Test2.__repr__
>>> print(repr(Test2()))
Test2.__repr__
>>> print(str(Test2()) == repr(Test2()))
True
```

通过上述两个例子的对比，我们可以知道: **当仅定义 <code>__repr__</code> 的时候， <code>__repr__</code> == <code>__str__</code>, 但是仅定义 <code>__str__</code> 的情况下，两者不相等**

## 两者均定义

```python
class Test3:
	def __repr__(self):
    	return "Test3.__repr__"
    def __str__(self):
    	return "Test3.__str__"


print(str(Test3()))
print(repr(Test3()))
print(str(Test3()) == repr(Test3()))
```



输出的结果如下:

```
>>> print(str(Test3()))
Test3.__str__
>>> print(repr(Test3()))
Test3.__repr__
>>> print(str(Test3()) == repr(Test3()))
False
```

显然当两者均定义的时候，会各自使用自己的方法

# 两者的区别

1.	<code>__str__</code> 用于为最终用户创建输出，而 <code>__repr__</code> 主要用于调试和开发。 <code>__repr__</code> 的目标是明确无误，<code>__str__</code> 是可读的
2.  <code>__repr__</code> [用于推断对象的"官方"字符串表示形式](https://docs.python.org/3/reference/datamodel.html#object.__repr__)（包含有关对象的所有信息的表示, <code>__str__</code> [用于推断对象的“非正式”字符串表示形式](https://docs.python.org/3/reference/datamodel.html#object.__str__)（对打印对象有用的表示形式


再通过一个例子说明问题:

```
import datetime
today = datetime.datetime.now()
 
print str(today)
print repr(today)  
```

输出结果如下:

```
>>> print(str(today))
2018-08-22 16:52:37.403320
>>> print(repr(today))
datetime.datetime(2018, 8, 22, 16, 52, 37, 403320)
```

## 参考链接

[Difference between \__str___ and \__repr__?](https://stackoverflow.com/questions/1436703/difference-between-str-and-repr)

[str() vs repr() in Python](https://www.geeksforgeeks.org/str-vs-repr-in-python/)

[object.\__repr__](https://docs.python.org/3/reference/datamodel.html#object.__repr__)

[object.\__str__](https://docs.python.org/3/reference/datamodel.html#object.__str__)






