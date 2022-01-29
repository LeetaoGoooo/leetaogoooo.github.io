---
title: 从发布-订阅模式谈谈 Flask 的 Signals
tags: [Flask,Flask Web 开发,Python]

date: 2019-06-24
published: true
hideInList: false
feature: 
isTop: false
---





# 发布-订阅模式

**发布-订阅模式**,顾名思义,就像大家订报纸一样,出版社发布不同类型的报纸杂志不同的读者根据不同的需求预定符合自己口味的的报纸杂志,付费之后由邮局安排人员统一派送. 

![](http://ww1.sinaimg.cn/large/006wYWbGly1g4cgqcjzuvj30e102c3yp.jpg)

上面一段话,提到了发布-订阅模式三个比较重要的点:

1.  发布者:报社
2.  订阅者:读者
3.  调度中心:邮局

不难看出上述过程中出版社和读者完全没有任何接触,在他们没有感知到对方的情况下通过邮局完成了整个流程,邮局就是传说中的中介(Broker)

![](http://ww1.sinaimg.cn/large/006wYWbGly1g4cgqtera9j30ci07ewer.jpg)

那么使用发布-订阅模式的有什么优点呢?这里就简单的说两点:**松耦合**,**可拓展性**,稍后通过例子进行讲解. 关于更深入的理解可以参考:

1.  [Publish/Subscribe-MSDN](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/ff649664(v=pandp.10))
2.  [Publish/Subscribe-Wikipedia](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern#Message_filtering)

对这个模式有所了解之后，让我们再回到 Flask 的 Signals

# Flask - Signals

## 说明

有了前面这个铺垫，不难意识到 Flask 的 Singals 其实就是我们上面说的**发布－订阅模式**的实现．官方文档对 Signals 的介绍过于简单，容易让初学者直接忽略过去，但是实际上这知识点十分重要，尤其在开发比较复杂的系统中，正确地使用 Singals 能够帮助我们实现系统的**松耦合**.

这种松耦合是通过某些行为被触发时，自动发送定义好的一种信号，与这个信号绑定的一些业务逻辑或行为，接收到这个信号后，会自动执行各自相应的业务逻辑。这些行为的产生者就是我们在发布订阅模式中**发布者**,通过**调度中心**,消息被转发到相应的**订阅者**,然后每个订阅者执行自己的逻辑,互不干扰.

就像我们在发布-订阅模式看到的那样,我们可以随时添加**订阅者**. 同样地,与该信号绑定的业务逻辑，可以是我们事先预定义好的，也可以是在后续开发中随需求变动新增上去的. 在基于 Signals 的机制下,系统会更加稳定和**可扩展**，也使得系统的业务逻辑更加清晰.

既然有这么多好处,那么该怎么使用呢?别着急,看一下几个例子.

## 例子

### Signal 的创建

两行代码就可以创建 Singals

```python
 from blinker import signal
 test= signal('test')
```

不过 Flask 文档中有另外一种写法

```python
from blinker import Namespace
my_signals = Namespace()
model_saved = my_signals.signal('model-saved')
```

两者本质上是没有任何区别的,原因我们可以看一下 blinker 的源码

```python
# https://github.com/jek/blinker/blob/master/blinker/base.py
signal = Namespace().signal
```

很显然从源码看两者基本上可以等价起来,前者只是帮助我们简化了一个步骤

### Signal 的发送

signal 创建好了之后,接下来就是使用了,使用很简单通过调用 **send()** 函数.需要注意的是,官方文档给了一个建议:

>Try to always pick a good sender. If you have a class that is emitting a signal, pass self as sender. If you are emitting a signal from a random function, you can pass current_app._get_current_object() as sender.

也就说明我们在实际使用过程中,最好将 **send()** 函数的第一个参数为 signal 的发送者

1. 在类中发送者(sender) 为 self

```python
class Model(object):
    def save(self):
        model_saved.send(self)
```

2. 在函数中发送者(sender) 为 current_app._get_current_object()

```python
def save():
    model_saved.send(current_app._get_current_object())
```

发送完消息,消息需要有人看,自然需要订阅者了.

### Signal 的订阅

订阅指定的 signal 可以通过使用 **connect()** 函数,当通过 send() 发送 signal 时,会自动触发这些订阅者,然后执行相应逻辑,从而完成相应的功能. 使用起来很简单,只需要给指定的函数加上一个 **connect_via** 或者 **connect** 的装饰器就可以了 

```python
# connect_via
from flask import Flask,current_app
app = Flask(__name__)

from blinker import Namespace
my_signals = Namespace()
test = my_signals.signal('test')

@test.connect_via(app)
def subscriber(sender,**kwargs):
    print(f'Got a signal sent by {sender},{kwargs}')

@app.route('/')
def hello_world():
    test.send(current_app._get_current_object(),data=3)
    test.send('test')
    return 'Hello, World!'

if __name__ == '__main__':
    app.run()
```

让我们执行一下然后看一下结果:

```
 * Serving Flask app "test" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 326-510-904
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
Got a signal sent by <Flask 'test'>,{'data': 3}
127.0.0.1 - - [24/Jun/2019 15:07:31] "GET / HTTP/1.1" 200 -
```

似乎少了依次输出?别着急,我们修改一下这个例子,使用 **connect**

```python
#  connect
@test.connect
def subscriber(sender,**kwargs):
    print(f'Got a signal sent by {sender},{kwargs}')
```

再次执行看一下结果

```
 * Serving Flask app "test" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 326-510-904
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
Got a signal sent by <Flask 'test'>,{'data': 3}
Got a signal sent by test,{}
127.0.0.1 - - [24/Jun/2019 15:10:43] "GET / HTTP/1.1" 200 -
```



#### connect_via 和 connect 

从上面最后一次输出,不难发现输出两次了,为什么第一次的时候只输出了一次呢? 很显然这就是  **connect_via** 和 **connect**  的区别,从上面的例子,我们不难看到, **connect_via** 多了一个参数,这个参数就是 sender,**使用 connect 的订阅方式并不支持订阅指定的发布者,如果我们需要订阅指定的发布者需要使用 connect_via(sender)**

# 最后

Signals 是个好东西,大家应该学会使用它.

# 参考

1.  [发布-订阅模式解释](https://blog.csdn.net/coderyjz/article/details/79422267)

2.  [Publish/Subscribe-MSDN](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/ff649664(v=pandp.10))

3.  [Publish/Subscribe-Wikipedia](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern#Message_filtering)

4.  [bliker](https://github.com/jek/blinker/blob/master/blinker/base.py#L460)

5.  [Signals](http://flask.pocoo.org/docs/0.12/signals/)