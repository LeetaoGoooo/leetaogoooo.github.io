---
title: Jinjia2 自定义 filter
tags: [Python,Flask]

date: 2018-10-16
published: true
hideInList: false
feature: 
isTop: false
---







# 前言

用过 <code>flask</code> 的人肯定对 <code>jinjia2</code> 不会陌生,自然对 <code>Jinjia2</code> 自带的 <code>filter</code> 也有印象，但是其自带的 filter 实在有限，有些时候就不得不需要我们自己定义 <code>filter</code> 了,接下来的例子将介绍如何自定义 <code>filter</code>

# 自定义 filter

## 举个例子
自定义 filter 其实很简单，写一个函数实现你需要的功能，然后添加到 jinjia_env 中，这里以一个返回列表长度到自定义函数为例子

```python
# test.py
from flask import Flask
from flask import render_template

app = Flask(__name__)

def my_len(arg):
    return len(arg)

env = app.jinja_env
env.filters['my_len'] = my_len 

@app.route("/")
def index():
    test_str = "hello"
    return render_template("index.html", test_str=test_str)

if __name__ == "__main__":
    app.run()
```

ok 函数定义完了，接下来就是在模版中去使用了.

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>jinjia2模板测试</title>
</head>
<body>
    <h1>len: {{test_str|my_len}}</h1>
</body>
</html>
```

简单吧？到这里似乎就可以结束了？没问题，但是实际开发中大家很多人的目录结构都不是这样吧，在文件里有着为了优雅，有着一些
<code>xxx.init_app(app)</code> 的代码吧？所以接下来让我们试着把这些 <code>filter</code> 集成一下。

## 让 filter 优雅起来

尝试将这些 <code>filter</code> 封装到一个类中并且也实现 <code>init_app</code> 方法

```python
# examples.py
class Example:
    @staticmethod
    def init_app(app):
        @app.template_filter('my_len')
        def my_len(arg):
            return len(arg)
```

然后就可以在其他文件中像这样子使用了

```python
from examples import Example
example = Example()

# ... 省略部分代码
def create_app(app):
    example.init_app(app)
```

# 题外话 —— 自定义 function

有时候我们需要一个函数处理数据返回结果使得我们可以进行下一步操作，这个时候就需要用到 <code>jinjia2</code> 的自定义函数。

## 自定义 function

自定义函数的方法很简单，通过 <code>add_template_global</code> 即可

```python
# 第一种写法
def my_len(arg):
    return len(arg)

app.add_template_global(my_len,'my_len')

# 第二种写法
@app.template_global('my_len')
def my_len(arg):
    return len(arg)

# 同样可以参照 filter 中封装到类中去
```

在模板中调用方法也与 <code>filter</code> 类似。


```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>jinjia2模板测试</title>
</head>
<body>
    <h1>len: {{my_len(test_str)}}</h1>
</body>
</html>
```

用法很简单，那么问题来了，这两者有什么区别吗？

# Jinjia2 的 function 和 filter 的区别

区别很简单，相较于 <code>function</code> 来说, <code>filter</code> 拥有访问上下文以及环境的能力，当然为了解决这个问题，jinjia2 有 [contextfilter ](http://jinja.pocoo.org/docs/2.10/api/#utilities) 等相关函数。


# 参考链接

[Jinjia2](http://jinja.pocoo.org/docs/2.10/api/)

[Difference between jinja2 functions and filters?](https://stackoverflow.com/questions/22491510/difference-between-jinja2-functions-and-filters)