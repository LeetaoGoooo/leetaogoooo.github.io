---
title: 多线程下的list
tags: [Python,Python 基础]

date: 2019-09-11
published: true
hideInList: false
feature: 
isTop: false
---



# 前言

`list` 是  `Python`  常用的几个基本数据类型之一.正常情况下我们会对 `list` 有增删改查的操作,显然易见不会有任何问题.那么如果我们试着在**多线程**下操作`list` 会有问题吗?

# 多线程下的 list

## 安全 or 不安全？ 不安全！

通常我们说的线程安全是指针对某个数据结构的所有操作都是线程安全，在这种定义下，Python 常用的数据结构 `list,dict,str` 等都是线程不安全的

尽管多线程下的 `list` 是线程不安全的，但是在 `append` 的操作下是它又是线程安全的.

## 如何判断线程安全呢？

对于线程安全不安全，我们可以通过极端条件下去复现，从而得出结论。比如说判断 `list` 是否线程安全

```python
import threading
import time

# 随意设置 count 的值，值越大错误抛出的越快
count = 1000
l = []

def add():
    for i in range(count):
        l.append(i)
        time.sleep(0.0001)

def remove():

    for i in range(count):
        l.remove(i)
        time.sleep(0.0001)


t1 = threading.Thread(target=add)
t2 = threading.Thread(target=remove)
t1.start()
t2.start()
t1.join()
t2.join()
print(l)
```

有时候一次运行并不一定就会出错，多次重试之后会出现类似下面的错误

![Image.png](http://ww1.sinaimg.cn/large/d9e82fa4ly1g6vnjzuaxfj20m6054jrw.jpg)

很显然这种操作方式不具有普适性，如果要是欧气太强，说不定会一直不出现异常。

那么出了这种方式，有没有比较简单有效的方法吗？答案是有的

## dis

dis 库是 Python 自带的一个库，可以用来分析字节码。这里我们需要有这样的认识，**字节码的每一行都是一个原子操作，多线程切换就是以原子操作为单位的，如果一个操作需要两行字节码就说明它是线程不安全的**

### remove

这里我们先看一下上面 `list` 的 `remove` 操作

```python
>>> import dis
>>> def test_remove():
...     a = [1]
...     a.remove(0)
... 
>>> dis.dis(test_remove)
  2           0 LOAD_CONST               1 (1)
              2 BUILD_LIST               1
              4 STORE_FAST               0 (a)

  3           6 LOAD_FAST                0 (a)
              8 LOAD_ATTR                0 (remove)
             10 LOAD_CONST               2 (0)
             12 CALL_FUNCTION            1
             14 POP_TOP
             16 LOAD_CONST               0 (None)
             18 RETURN_VALUE
```

从上面不难看出，整个 `remove` 操作被分成了好几条指令，这就意味着在多线程情况下会出现错乱的情况，试想一下，如果多线程下都去 `remove` 列表的话，并且不按照顺序，很容易出现问题。

### append

在最上面我们说到，`list` 的 `append` 操作是线程安全的，那么究竟是为什么呢？我们同样来用 `dis` 查看一下

```python
8          19 LOAD_GLOBAL              0 (a)
           22 LOAD_ATTR                2 (append)
           25 LOAD_CONST               2 (1)
           28 CALL_FUNCTION            1
           31 POP_TOP    
```

这里显然，`append` 也是有几条指令，势必在多线程执行的情况下也会发生交错，但是对于多线程下我们操作 `append`， 我们肯定也不会在乎这个时候 `list` 到顺序问题了，所以我们说它的 `append` 是线程安全的

# 参考

[what-kinds-of-global-value-mutation-are-thread-safe](https://docs.python.org/3/faq/library.html#what-kinds-of-global-value-mutation-are-thread-safe)

[are lists thread safe](https://stackoverflow.com/questions/6319207/are-lists-thread-safe/19728536#19728536)





