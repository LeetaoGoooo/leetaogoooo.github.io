---
title: context_processor 上下文处理器
tags: [Python,Flask]

date: 2018-11-21
published: true
hideInList: false
feature: 
isTop: false
---







# 前言

写专栏写了一段时间了，但是一直都没有带大家做一个完整的小项目，俗话说的好，“光说不练假把式,光练不说真把式,连说带练全把式”，自然我要当仁不让做“全把式”了，然后自己就偷偷摸摸的花了几天时间撸了一个博客，功能暂时没有全部完成，完成后会写一个教程教大家从零开始撸一个自己的博客，博客地址:http://132.232.71.126:8080/, 测试账号(没有修改、创建、删除权限): test/test,大家可以先睹为快， 好了这都不是重点，重点是我在写的过程中遇到了一个问题，我的好几个页面都有一个标签云，这个标签云中的标签自然会随着博客内容的增多而变化，因此肯定需要根据博客内容，自动生成，那么问题来了，我改怎么写呢？

# 二话不说，先写为敬

博客里面有三个地方用到了标签云：主页面，分类页面，博客详情页面，于是有了下面一段代码

```python

# 主页面
@main.route("/")
def index():
    tags = Tag.query.all()
    # ...省略部分代码
    return render_html("index.html", tags=tags,)

# 分类页面
@main.route("tag/<int:id>")
def tags(id):
    tags = Tag.query.all()
    # ...省略部分代码
    return render_html("tags.html", tags=tags,)

# 博客详情页面
@main.route("post/<int:id>")
def post(id):
    tags = Tag.query.all()
    # ...省略部分代码
    return render_html("post.html", tags=tags,post=post)
```

好像问题解决了？所有页面都能显示出来标签云了？ 但是这三个页面都回传 **tags** 会不会太难看了一些，完全不优雅了嘛，有没有好的办法呢？ 答案自然是有的，接下来就到我们的重点了。

# context_processor 闪亮登场

跟我们之前的说到的钩子函数一样，它也有一个兄弟--**app_context_processor**，区别很简单，后者是针对蓝图的。让我们看一下它的官方定义:

> Registers a template context processor function.

翻译过来很简单: 注册模板上下文处理器功能。 这个真能解决我们的问题吗？别着急让我们试一下，把之前的代码改造一下。

```python
@main.app_context_processor
def peach_blog_menu():
    tags = Tag.query.all()
    return dict(tags=tags)

@main.route("/")
def index():
    # ...省略部分代码
    return render_html("index.html")

# 分类页面
@main.route("tag/<int:id>")
def tags(id):
    # ...省略部分代码
    return render_html("tags.html")

# 博客详情页面
@main.route("post/<int:id>")
def post(id):
    tags = Tag.query.all()
    # ...省略部分代码
    return render_html("post.html")
```

是不是发现什么消失了？ **tags** 好像从之前的几个函数中消失了，没有回传到前台，能访问到吗？自然是可以的。原因自然是 context_processor 了，它可以**将我们的定义变量在所有模板中可见**。

**如何使用呢？**
1.  如上述代码那样， context_processor 作为一个装饰器修饰一个函数
2.  函数的返回结果必须是 *dict*, 然后其 *key* 将会作为变量在所有模板中可见

当你的很多视图函数中需要回传一个相同的变量的时候，这个时候就可以考虑使用 context_processor 了

