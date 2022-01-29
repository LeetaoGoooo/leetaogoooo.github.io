---
title: Flask的g对象和钩子函数
tags: [Python,Flask]

date: 2018-10-30
published: true
hideInList: false
feature: 
isTop: false
---







# 前言

说到钩子函数大家可能会觉得有点陌生，但是不着急，接着看下去，大家只要用过 <code>flask</code> 对我接下来说的这些一定不会陌生。

# 钩子函数

钩子函数可以分为两层说明，第一层是 <code>app</code> 层，第二层则是 <code>blueprint</code> 层

## app 层

<code>app</code> 层的钩子函数有 <code>before_request</code>,<code>before_first_request</code>,<code>after_request</code>,<code>teardown_request</code>,下面我们一一分析。

### before_request

>Registers a function to run before each request.

在每次请求都会执行的函数，比如：用于连接数据库连接，或者从会话中加载登录用户

**注意**:该函数不需要任何参数，如果其返回了一个非空的值，则其将会作为当前视图的返回值,看下面的例子。

**例子**

```python
from flask import Flask,
 
app = Flask(__name__)
 
@app.before_request
def before_request():
    print('before request started')
    return "example01" 
 
@app.route('/')
def index():
    return 'Hello world'' 
 
if __name__ == '__main__':
    app.run(debug=True)
```

这个例子下当访问 <code>localhost:5000/</code>的时候，前端渲染的值为 “example01” 而不是 “Hello world”

### before_first_request

>Registers a function to be run before the first request to this instance of the application.

仅在第一次请求的时候去调用这个函数，比如初始化加载一次性的数据。

**注意**:和 <code>before_request</code> 不同的是, 它的非空返回值会被忽略。

**问题来了：before_first_request 和 before_request 加载顺序是什么样子呢？**

让我们通过下面的代码看一下:

**例子**:

```python
from flask import Flask, g, request
 
app = Flask(__name__)
 
@app.before_request
def before_request():
    print('before request started')
 
@app.before_first_request
def before_request():
    print('before first request started')

@app.route('/')
def index():
    return 'Hello'
 
if __name__ == '__main__':
    app.run(debug=True)
```

结果如下图:

这次 first_request 胜出了

![](http://ww1.sinaimg.cn/large/006wYWbGly1fwr87mt5p0j30kf02m749.jpg)

### after_request

> Register a function to be run after each request

在每次请求结束后运行

除了运行的时间和上面不同之外，after_request 这个函数带有一个参数，用来接收[response_class](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.response_class)，一个响应对象，**一般用来统一修改响应的内容，比如修改响应头。**

### teardown_request

>Register a function to be run at the end of each request, regardless of whether there was an exception or not.

在每次请求结束调用，**不管是否出现异常**，同样 teardown_request 也需要一个参数，这个参数用来接收异常，当然没有异常的情况下这个参数的值为 <code>None</code>,**一般它用来释放程序所占用的资源，比如释放数据库连接**

**after_request 和 teardown_request 的区别**

两者最大的区别是在于，从Flask 0.7开始，如果出现未处理的异常，<code>after_request</code> 将不会被执行,而后者将正常运行并接收异常，其次还有两者的执行顺序，让我们通过代码去了解一下.

**例子**

```python
from flask import Flask, g, request
 
app = Flask(__name__)
 
@app.before_request
def before_request():
    print('before request started, %s' % request.url)
 
@app.before_first_request
def before_request():
    print('before first request started, %s' % request.url)

@app.after_request
def after_request(reponse):
    print("after request started, %s" % request.url)
    return reponse

@app.teardown_request
def teardown_request(exception):
    print("teardown request,%s,%s" % (exception,request.url))

@app.route('/')
def index():
    return 'Hello'
 
if __name__ == '__main__':
    app.run(debug=True)
```

结果如下:

结果很清晰，<code>after_request</code> 先执行（注意这个图和代码）

![](http://ww1.sinaimg.cn/large/006wYWbGly1fwrgdsak0yj30l50440sy.jpg)

说完了，<code>app</code> 层的关于 <code>request</code> 的钩子函数，接下来说一说 <code>blueprint</code> 层的

## blueprint 层

blueprint 层关于request 的钩子函数其实和app层基本一致，有两点区别:

1. 与 app 层相比，bluepint 层少了一个 <code>before_first_request</code> 的钩子函数

2. 多了一些，可以在蓝图调用 app 层的钩子函数：before_app_first_request,before_app_request,after_app_request,after_app_request,teardown_app_request

## 请求上下文(request context)

为什么这里会突然出现这个标题呢？我们在哪里用到了这个吗？还记得上个图吗，我们在所有的请求中都访问了<code>request</code>对象，并且成功了输出了 <code>url</code>,

但是我们并没有传入它，它是一个全局对象吗？让我们测试一下:

```python
from flask import Flask, g, request
 
app = Flask(__name__)
 
def test_request():
    print("test request,%s" % request.url)

test_request()

@app.route('/')
def index():
    return 'Hello'
 
if __name__ == '__main__':
    app.run(debug=True)
```

看一下截图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fwrgkphzglj30m401y0sn.jpg)

由此可见 <code>request</code> 对象仅可以在请求上下文的生命周期可以访问，由此不难得出,我们上面说到几个钩子函数也是挂载到请求上下文的生命周期的不同阶段从而发挥作用的。

# g 对象

flask 中有两种上下文，一是：请求上下文(request context),上面刚刚说到的，另外一种则是应用上下文(application context),

g 对象则是在后者期间存储数据的命名空间对象。在**一个请求**之间用它来存储数据是再好不过了。

注意我加粗的地方：一个请求，为什么说是在一个请求之间呢，上面说了 g 是在应用上下文中发挥作用的，这就意味着一旦它所处的应用上下文结束了，它也随之失效了，那么一个应用上下文的生命周期是多久呢？

让我们看一下官网给的说明:

>The application context is created and destroyed as necessary. When a Flask application begins handling a request, it pushes an application context and a request context. When the request ends it pops the request context then the application context. Typically, an application context will have the same lifetime as a request.

看不懂上面英文没有关系，我来总结一些，上面一段官网文字说道，一个应用上下文的生命周期和一个请求相同，这意味什么？也就以为着当一个请求结束了，当前的 g 也就失效了。







