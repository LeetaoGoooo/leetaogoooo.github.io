---
title: 在Flask启动时启动后台任务
tags: [Flask,Flask Web 开发]

date: 2020-11-24
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
在实际过程开发中，有时候会有在启动 Web 服务的同时，希望启动一个后台任务去运行其他的任务。显然后台的任务，都是借由线程去完成的，所以在什么地方运行线程是需要我们解决的问题。
# 解决方案
## 请求时启动任务
这种方式显然时最简单的，配合 flask 自带的 api 我们可以在**第一次请求**时，甚至在**每次请求**启动线程。**
```python
import threading
import time
from flask import Flask
app = Flask(__name__)

# app.before_request etc
@app.before_first_request
def activate_job():
    def run_job():
        while True:
            print("执行后台任务...")
            time.sleep(3)
            
    thread = threading.Thread(target=run_job)
    thread.start()

@app.route("/")
def hello():
    return "Hello World!"


if __name__ == "__main__":
    app.run()
```
运行一下代码看一下结果：
```shell
(venv) D:\workspace\python\test>python test_flask.py
 * Serving Flask app "test_flask" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
执行后台任务...
127.0.0.1 - - [24/Nov/2020 16:02:22] "GET / HTTP/1.1" 200 -
127.0.0.1 - - [24/Nov/2020 16:02:22] "GET /favicon.ico HTTP/1.1" 404 -
执行后台任务...
执行后台任务...
```
显然这个有一点局限性，在没有任何请求的时候线程不会执行。我们可以对代码进行优化，让线程这部分独立于 Flask 应用。


## 和 Flask 应用一起启动
```python
import requests
import threading
import time
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"


def start_runner():
    def start_loop():
        while True:
            print("执行后台任务...")
            time.sleep(3)
            
    thread = threading.Thread(target=start_loop)
    thread.start()

if __name__ == "__main__":
    start_runner()
    app.run()
```
同样的，运行一下代码看一下结果：
```shell
(venv) D:\workspace\python\test>python test_flask.py
执行后台任务...
 * Serving Flask app "test_flask" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
执行后台任务...
执行后台任务...
```
这个时候，后台任务和 Flask 应用相互独立，功能算是基本实现。但是后台任务和 Flask 应用完全分离，实现方式不够优雅，我们对代码进行重构一下。
```python
import requests
import threading
import time
from flask import Flask
import os

class FlaskApp(Flask):
    def __init__(self, *args, **kwargs):
        super(FlaskApp, self).__init__(*args, **kwargs)
        self._activate_background_job()

    def _activate_background_job(self):
        def run_job():
            while True:
                print('执行后台任务')
                time.sleep(3)

        t1 = threading.Thread(target=run_job)
        t1.start()


app = FlaskApp(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run()
```


# 拓展思维
到这里程序算是基本上完成了，但是如果在运行过程中，停止应用的话，显然会让后台任务直接粗暴的退出，那么有没有一种优雅的方式，可以让应用程序在退出时，让后台任务收拾完成之后，优雅的退出呢？