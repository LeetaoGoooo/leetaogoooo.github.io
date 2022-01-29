---
title: Flask的源码系列——前身
tags: [Python,Flask,源码]

date: 2018-04-23
published: true
hideInList: false
feature: 
isTop: false
---








# What is Flask?

用了这么久的 Flask 框架,那么 Flask 究竟是什么呢？借用官方文档的一句话来说明这个问题:

>Flask is a lightweight WSGI web application framework.

不难理解上面的一句话: Flask 是一个基于 **WSGI** 的轻量级的应用框架.那么 WSGI 又是什么?

# What is WSGI ?

>The Web Server Gateway Interface (WSGI) is a simple calling convention for web servers to forward requests to web applications or frameworks written in the Python programming language. ——[ 1 ]
WSGI is the Web Server Gateway Interface. It is a specification that describes how a web server communicates with web applications, and how web applications can be chained together to process one request.
WSGI is a Python standard described in detail in PEP 3333. ——[ 2 ] 

<p>
上面三段话翻译过来的大致意思是:<p> Web 服务器网关接口（WSGI）是一种简单的调用约定，用于 Web 服务器将请求转发到用 Python 编程语言编写的 Web 应用程序或框架。
WSGI 是 Web 服务器网关接口。它描述了 web 服务器如何与 web 应用程序通信，以及如何将 web 应用程序链接在一起处理一个请求。
WSGI 是在 [**PEP 3333**](https://www.python.org/dev/peps/pep-3333) [3]中详细描述的 Python 标准。

## Why is WSGI necessary? 

最初的传统的 Web 服务器是没有办法运行 Python 的应用程序的。直到 1990年末,一个  Grisha Trubetskoy 的开发者提出了一个 [mod_python 的 Apache module](https://grisha.org/blog/2013/10/25/mod-python-the-long-story/)。然后再随后的几年和2000年初,apache 装载了 mod_python 模块,然后绝大多数的 python web 应用程序都得以运行。然而 mod_python 并不是一个标准的规格,随着 mod_module 开发的停滞不前和安全隐患被发现, python 社区意识到必须要有一个始终如一运行 python 代码的方法，因此 wsgi 应运而生。直到现在，WSGI仍然是运行 python web 应用程序的公认方法。

![](http://ww1.sinaimg.cn/large/006wYWbGly1fqmp1h5mwbj30mg09e74l.jpg)

如上图所示，WSGI 服务器只是调用在 PEP 3333 标准中定义的 WSGI 应用程序上的可调用对象。

## WSGI's Purpose

所以为什么不直接将 WSGI 服务器直接指向 WSGI 的应用框架呢?
    1. **WSGI 能够提供可好的可拓展性.**  基于 WSGI，开发人员可以随时将 Web 组件替换为其他的。比如说由 Green Unicorn 无缝切换到 uWSGI。 
    2. **WSGI服务器提升了负载规模** 一次为千个请求提供动态内容数是WSGI服务器，而不是框架。 WSGI服务器处理来自Web服务器的处理请求，并决定如何将这些请求传递给应用程序框架的进程。

![](http://ww1.sinaimg.cn/large/006wYWbGly1fqmsl2kaf4j30xr0b23yw.jpg)


## What is  PEP 3333  ?

PEP 3333 一直出现在上文中,那么 PEP 3333 的究竟说了什么呢,有兴趣的读者可以直接点击[PEP 3333](https://www.python.org/dev/peps/pep-3333)查看原文。嫌麻烦的话，可以直接看下图，一图胜千言,我花了一点时间将 PEP 3333 中的主要内容整理成了一个简单的脑图

![](http://ww1.sinaimg.cn/large/006wYWbGly1fqmnnn8ekjj324r1f9445.jpg)

# References

[ 1 ] [Web Server Gateway Interface](https://en.wikipedia.org/wiki/Web_Server_Gateway_Interface)

[ 2 ] [What is WSGI?](http://wsgi.readthedocs.io/en/latest/what.html)

[ 3 ] [PEP 3333](https://www.python.org/dev/peps/pep-3333)

[ 4 ] [WSGI Servers](https://www.fullstackpython.com/wsgi-servers.html)