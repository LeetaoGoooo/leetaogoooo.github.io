---
title: __name__ 是什么？
tags: [Python,Python 基础]

date: 2019-08-18
published: true
hideInList: false
feature: 
isTop: false
---





# 前言

在我们浏览一下 python 文件或者自己写 python 代码的时候，时常会在代码的最后加上这样的一行代码

```python
if __name__ == '__main__':
    func_name() 
```

那么这一行代码有什么具体的作用呢，不加的话会对我们的结果造成影响吗？


# `__name__`

首先对于用双下划线开头且结尾的变量，在 Python 中被称为**内置变量**，除了 `__name__`,我们常见的还有 `__init__`，`__dict__` 等等.那么有多少内置变量呢？我们可以通过下面在交互界面输入下面的命令，查看 Python 全部内置变量和内置函数

```python
>>> dir(__builtins__)
```

结果如下图：

![](http://ww1.sinaimg.cn/large/006wYWbGly1g63l5c96t6j30vo0pqkjl.jpg)

## 不同情况下的 `__name__` 的值 

首先我们需要知道 `__name__` 在不同情况下会有不同值，它的值取决于我们是如何执行脚本的.我们可以通过几个例子感受一下：

### Example 0

```python
# test.py
print(f'__name__ 在 test.py 值为 {__name__}')
```

然后直接执行一下代码

```python
$ python test.py
```

然后看一下输出

```python
$ python test.py 
__name__ 在 test.py 值为 __main__
```

在这个例子中，我们发现 `__name__` 的值是 `__main__`

### Example 1

在这个例子中，我们重新创建一个脚本 test1.py 然后我们在 test1.py 中调用 test.py

```python
# test1.py
import test
print(f'__name__ 在 test1.py 值为 {__name__}')
```

接着执行一下 test1.py,再看一下输出

```python
python test1.py 
__name__ 在 test.py 值为 test
__name__ 在 test1.py 值为 __main__
```

结果是不是很有意思？整个过程是什么样子的呢？简单的画了一个图

![](http://ww1.sinaimg.cn/large/006wYWbGly1g63l5rf79kj30q60e174f.jpg)


## 什么时候使用 `__name__`

有时候，我们用 Python 写了一个脚本，当我们既希望这个脚本可以单独运行，同样希望它可以在其他的脚本中发挥作用. 这个时候就需要考虑使用 `__name__` 了. 这里通过改造上面 Example 1的例子来直观感受一下

修改一下 test.py 文件
```python
# test.py
def hello(name):
    print(f'Hello,{name}')
 

if __name__ == '__main__':
    hello("test")
```

再修改一下 test1.py 文件

```python
# test1.py
from test import hello
hello("test1")
```

然后让我们先尝试直接运行一下 `test.py`,很显然这个时候, if 语句条件满足，会输出 **Hello,test**

```
$ python test.py 
Hello,test
```

这个时候我们如果运行 `test1.py`,程序就会输出 **Hello,test1** 了

```
$ python test1.py 
Hello,test1
```

如果我们把 `if __name__ == "__main__"` 在 `test.py` 去掉会发生什么呢？

```
$ python test1.py 
Hello,test
Hello,test1
```

# 参考

[What does if __name__ == “__main__”: do?](https://stackoverflow.com/questions/419163/what-does-if-name-main-do)

[What’s in a (Python’s) __name__?](https://medium.com/free-code-camp/whats-in-a-python-s-name-506262fe61e8)