---
title: 'functools.lru_cache 的正确用法'
date: 2022-01-26 17:29:53
tags: [Python]
published: true
hideInList: false
feature: 
isTop: false
---
# 前言

 >lru_cache 是 functools 库中的一个函数，它为函数提供缓存功能的装饰器，缓存 maxsize 组传入参数，在下次以相同参数调用时直接返回上一次的结果。

从它的功能来说是一个不错的方法，可以在一定程度上提高函数的运行速度，但是它存在一个问题，**当你用functools.lru_cache装饰器来装饰一个实例方法时，封装该方法的类的实例在持有它们的进程中永远不会被垃圾回收。**

## 验证

接下来让我们通过一个简单的例子去论证上述的观点。（以下代码运行于 python3.6.8）

``` python

# test.py

  

import time

from functools import lru_cache

from typing import TypeVar

  

Number = TypeVar("Number", int, float, complex)

  
  

class TestLruCache:

	def __init__(self, delay: int = 1) -> None:
	
		self.delay = delay
	
	  
	
	@lru_cache(maxsize=128)
	
	def calculate(self, *args: Number) -> Number:
		
		time.sleep(self.delay)
		
		return sum(args)
	
	  
	
	def __del__(self) -> None:
	
		print("Deleting instance ...")

  
  

# 创建一个实例

test = TestLruCache(2)

  

# 统计运行时长

start_time = time.perf_counter()

result = test.calculate(1, 2)

end_time = time.perf_counter()

print(f"计算耗时 {end_time-start_time} s, result: {result}.")

  
  

start_time = time.perf_counter()

result = test.calculate(1, 2)

end_time = time.perf_counter()

print(f"计算耗时 {end_time-start_time} s, result: {result}.")

```

  

这里我们创建一个类，这个类有个 `calculate` 方法，这个方法使用 `lru_cache` 装饰，为了更好的体现 `lru_cache`的作用，加上了一个 `delay` 的参数，其中 `__del__` 的魔术方法作用就是当开始垃圾回收的时候，`__del__` 方法就会被执行。

接下来我们尝试运行该程序，看一下输出结果:

  

``` bash

计算耗时 2.0021407306194305 s, result: 3.

计算耗时 4.209578037261963e-06 s, result: 3.

Deleting instance ...

```

  

从输出结果不难看的出，使用 `lru_cache` 后，第二次调用 `calculate` 方法，运行的时间降低了很多，不难理解，第二次我们请求的参数完全一样，所以结果直接从字典中就获取到了。但是需要注意到一点，在程序的生命周期中，`TestLruCache` 的实例被没有被垃圾回收。

上面的例子可能说服力还不够，没关系，接下来我们再看几个例子。

  

## 垃圾回收没有生效

  

如果我们通过 `python -i` 的方式执行上述的脚本，你就清晰地发现垃圾回收没有生效。

  

``` python

$ python3 -i test.py

计算耗时 2.004133677983191 s, result: 3.

计算耗时 3.143970388919115e-06 s, result: 3.

>>> import gc

>>>

>>> test.calculate(1,2)

3

>>>

>>> test = None

>>>

>>> gc.collect()

22

```

  

在交互模式下，我将 `test` 赋值为 `None`，并且主动调用垃圾回收器，能看到 `gc.collect()` 的输出结果为 `22`，但是并没有看到 `__del__` 方法的输出，这说明程序里依然存在 `TestLruCache` 的实例引用，垃圾回收器并不能将对象成功的回收。

让我们排查一下究竟是谁还在引用实例

  

``` python

$ python3 -i test.py

计算耗时 2.004039512015879 s, result: 3.

计算耗时 3.3050309866666794e-06 s, result: 3.

>>> test.calculate.cache_info()

CacheInfo(hits=1, misses=1, maxsize=128, currsize=1)

>>> test.calculate(1,2)

3

>>> test.calculate.cache_info()

CacheInfo(hits=2, misses=1, maxsize=128, currsize=1)

>>> test.calculate.cache_clear()

>>> test = None

Deleting instance ...

```

  

上面的 `cache_info` 输出结果表明，这个缓存保存着实例的引用，除非手动清除。当我们手动清除缓存并将变量 `test` 重新分配为 `None` 时，垃圾收集器才会删除该实例。

除此之外，在这个例子里我们设置的 `maxsize` 是 `128`，表明最大缓存的结果个数是 `128` 个，一旦我们将 `maxsize` 置为 `None` 的话，LRU 特性将被禁用且缓存可无限增长。在这情况下将会很危险，一旦我们在一次请求中创建很多实例，这些实例不能为垃圾回收，一旦达到一定程序，必然会导致内存溢出，从而造成程序崩溃。

那么能不能解决这个问题呢？别着急，往下看

  

# 解决方案

  

解决这个问题很简答，我们只需要让缓存成为实例的本地变量就行了，这样一来，从缓存到实例的引用就会随着实例一起被删除。看一下改造后的例子:

``` python

import time

from functools import lru_cache

from typing import TypeVar

  

Number = TypeVar("Number", int, float, complex)

  
  

class TestLruCache:

	def __init__(self, delay: int = 1) -> None:
	
		self.delay = delay
		
		self.calculate = lru_cache(maxsize=128)(self._calculate)
	
	  
	
	def _calculate(self, *args: Number) -> Number:
	
		time.sleep(self.delay)
		
		return sum(args)
	
	  
	
	def __del__(self) -> None:
	
		print("Deleting instance ...")

```

  

好了让我们验证一下改造后的程序是否可以:

  

``` python

$ python3 -i test.py

>>> test = TestLruCache(2)

>>> test.calculate(1,2)

3

>>> test.calculate.cache_info()

CacheInfo(hits=0, misses=1, maxsize=128, currsize=1)

>>> import gc

>>> test = None

>>> gc.collect()

Deleting instance ...

30

```

注意到这次，我们不需要主动去清除缓存，但是需要显示的调用 `gc.collect()` 去执行垃圾回收。这是因为这种诡计创造了循环引用，垃圾回收需要做一些特殊的魔术来清除内存。在真正的代码中，Python 解释器会在后台为我们清理这些，而不需要我们调用垃圾回收。

  

# 类方法和静态方法

  

上面提到的问题，会对类方法和静态方法产生影响吗？**答案是否定的**，让我们验证一下：

  

``` python

# -*coding: utf-8 -* # test1.py

  

from functools import lru_cache

import time

  
  

class Test:

	@classmethod
	
	@lru_cache(maxsize=128)
	
	def test(cls, delay: int) -> int:
		
		cls.delay = delay
		
		time.sleep(delay)
		
		return 42

  

	def __del__(self) -> None:
	
		print("Deleting instance ...")

  
  

test_1 = Test()

test_2 = Test()

  
  

start_time = time.perf_counter()

result = test_1.test(2)

end_time = time.perf_counter()

print(f"耗时 {end_time start_time} s, result: {result}.")

  
  

start_time = time.perf_counter()

result = test_2.test(2)

end_time = time.perf_counter()

print(f"耗时 {end_time start_time} s, result: {result}.")

```

  

同样的，我们在交互模式下验证结果

  

``` python

$ python -i test1.py

耗时 2.003696349042002 s, result: 42.

耗时 4.3199979700148106e-06 s, result: 42.

>>> test_1 = None

Deleting instance ...

>>> test_2 = None

Deleting instance ...

```

  

同理我们也可以验证一下静态方法，基本上一样，这里就提供一下代码

  

``` python

from functools import lru_cache

import time

  
  

class Test:

	@staticmethod
	
	@lru_cache(maxsize=128)
	
	def test(cls, delay: int) -> int:
	
		cls.delay = delay
		
		time.sleep(delay)
		
		return 42
	
	  
	
	def __del__(self) -> None:
	
		print("Deleting instance ...")

```

  

# 参考链接

[functools.lru_cache](https://docs.python.org/3.6/library/functools.html#functools.lru_cache)
[Python LRU cache in a class disregards maxsize limit when decorated with a staticmethod or classmethod decorator](https://stackoverflow.com/questions/70409673/python-lru-cache-in-a-class-disregards-maxsize-limit-when-decorated-with-a-stati)

