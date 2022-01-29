---
title: Flask 中的 session 的故事
tags: [Flask]

date: 2019-03-12
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

凡是使用过 Flask,可能对 Flask 中  session 不陌生, 我们通常用 session 保存特定的用户信息,从而达到在 request 请求之间共享数据的目的,需要注意的一点 flask 中的 session 是基于 cookies 实现的,也就意味着着受制于 cookies,一旦出于某种原因 cookies 失效了,也就意味着 session 也会随之失效. 


# 使用

关于 session 的使用方法也很简单, **session[key] = value**, 但是也存在需要注意的地方,接下来看一个例子

## 例子

```python
from flask import Flask, session
app = Flask(__name__)
app.secret_key = "123"  # 使用 session 之前,必须要设置 secret_key

@app.route('/')
def create(): #1
    session['xxx'] = ['1','2','3']
    print(",".join(session['xxx']))
    return ",".join(session['xxx'])

@app.route('/rm/<id>')
 def rm(id): #2
    session['xxx'].remove(id)
    print(",".join(session['xxx']))
    return ",".join(session['xxx'])

@app.route('/add/<id>')
def add(id): #3
    session['xxx'].append(id)
    print(",".join(session['xxx']))
    return ",".join(session['xxx'])

@app.route('/test')
def out_print(): #4
     print(session['xxx'])
     print(",".join(session['xxx']))
     return ",".join(session['xxx'])
     
if __name__ == '__main__':
    app.run()
```

有趣的事情来了,我们按照 #1, #2(请求参数为2),#4, #3(请求参数为4), #4 的方式来请求 url,那么返回结果依次应该是什么呢?

## 结果

```
1,2,3
1,3
1,2,3
1,2,3,4
1,2,3
```

看一下截图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1g104yjiipoj30p2085gly.jpg)


## 原因

是不是觉得很奇怪,为什么我对 session 的修改没有生效? 这是因为 session 中有一个属性叫做 **modified**, 如果 session 发现其值有修改，如果修改的对象是可变的对象，则改修不会自动生效，除非**手动设置 modified = True**，也就是说我们需要上述的例子中 session 及时的被修改，需要在修改值后加入如下代码：

```python
 session.modified = True
```

# 彩蛋

对于上述例子，在执行第二步之前，大家可以打开 Google 浏览器调试模式，找到 Application tab 页下的 Cookies，会发现里面有个 session-id 的 cookies，大家可以试着把它修改或者清空后再执行步骤 2，会有意想不到的彩蛋哟


# 参考

[Flask.session](http://flask.pocoo.org/docs/1.0/api/#flask.session)

