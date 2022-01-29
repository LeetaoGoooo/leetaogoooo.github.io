---
title: 什么是 __future__ 模块 ？
tags: [Python]

date: 2019-01-07
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

不知道大家有没有注意到 python 有一个  \_\_future\_\_  模块，如果大家有人看开源项目兼容 py2 和 py3 的话，它们的源代码中会经常出现这个模块，那么这个模块究竟有什么作用呢？

# \_\_future\_\_

关于 \_\_future\_\_ 的作用，官方文档中也有明确提到，让我们看一下:

> \__future\__ is a real module, and serves three purposes:
1. To avoid confusing existing tools that analyze import statements and expect to find the modules they’re importing.
2. To ensure that future statements run under releases prior to 2.1 at least yield runtime exceptions (the import of \_\_future\_\_ will fail, because there was no module of that name prior to 2.1).
3. To document when incompatible changes were introduced, and when they will be — or were — made mandatory. This is a form of executable documentation, and can be inspected programmatically via importing \_\_future\_\_ and examining its contents.

翻译过来大致意思如下:

1. 为了避免混淆现有工具，分析import语句和期望找到他们要导入的模块。
2. 确保2.1之前的版本导入 \_\_future\_\_产生运行时异常，因为2.1之前没有这个模块
3. 记录何时引入了不兼容的更改，以及何时将其强制执行。 这是一种可执行文档，可以通过导入 \_\_future\_\_进行可编程式检查。

           
看完的感觉是不是这样的？

![](http://ww1.sinaimg.cn/thumbnail/006wYWbGly1fyy8rbifu0j30jg0eoab9.jpg)

如果是的话，就对了。别看官方文档上面写的很邪乎，其实总结起来很简单， python 在新版本会引入某些新的功能特性，但是有的时候有些改动是不兼容旧版本的，举个例子说，比如你的项目是用的是 py2.7，现在你需要迁移到 3.x 版本，直接升级到 3.x 肯定是不行的，那怎么办呢？这个时候就需要用到 \__future\__ 了，我们可以通过 \__future\__ 导入新版本某些模块，测试新版本的新功能，等测试成功后再升级到新的版本上.

# 例子

说完了，我们举个最典型的例子， **print**, 使用过 py2.7 和 py3.x 的小伙伴肯定都知道，在 3.x 的时候，print 成了一下函数，那么让我们在 py2.7 通过 \_\_future\_\_ 感受一下

![](http://ww1.sinaimg.cn/large/006wYWbGly1fyy9575xrej30bc04qdg1.jpg)

除了 print，还有其他不少类似的，在官方文档中都有提及到，我这里就简单截个图，有兴趣的可以自己去官网研究一下

![](http://ww1.sinaimg.cn/large/006wYWbGly1fyy97sxesuj30lo075abh.jpg)

# 参考链接

[\_\_future\_\_ — Future statement definitions](https://docs.python.org/3.6/library/__future__.html?highlight=__future__#module-__future__)

[What is \_\_future\_\_ in Python used for and how/when to use it, and how it works](https://stackoverflow.com/questions/7075082/what-is-future-in-python-used-for-and-how-when-to-use-it-and-how-it-works)
