---
title: special method  __call__ python
tags: [Python]

date: 2018-04-26
published: true
hideInList: false
feature: 
isTop: false
---







记得当初写作文的时候,老师强调三w的原则,接下来我们讨论 __call__ 也按照这个原则。

# What is __call__
没有什么比官方手册更权威的东西了,看一下官方是如何解释的
>Called when the instance is “called” as a function; if this method is defined, x(arg1, arg2, ...) is a shorthand for x.__call__(arg1, arg2, ...). —— [ 1 ]

用调用函数一样调用实例; 如果定义了此方法，则x（arg1，arg2，...）是x .__调用__（arg1，arg2，...）的简写形式。

# how to use __call__

我们简单举三个例子

```python
class SumTwo:
    def __init__(self):
        pass    
    def __call__(self,one,two):
        return (one+two)

sum_two = SumTwo()
print(sum_two(1,2))
```

看一下结果:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fqq9jw9tnqj30ep0253yd.jpg)


然后试着实现一下**斐波那契数列**

```python
class Fib:

    def __init__(self):
        self.fib = [0,1]
    
    def __call__(self,n):
        if n <= 1:
            return self.fib[n]
        else:
            for i in range(2,n):
                tmp_value = self.fib[i-1] + self.fib[i-2]
                self.fib.append(tmp_value)
        return self.fib


fib = Fib()

for i in fib(10):
    print(i)
```

输出如下图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fqq6eplhiwj30gv07n3yi.jpg)

最后以我们十分熟悉的**阶乘**再收尾:

```python
class Factorial:
    def __init__(self):
        self.cache = {}
    def __call__(self, n):
        if n not in self.cache:
            if n == 0:
                self.cache[n] = 1
            else:
                self.cache[n] = n * self.__call__(n-1)
        return self.cache[n]

factorial = Factorial()

for i in range(10):                                                             
    print("{}! = {}".format(i, factorial(i)))
```
输出如下图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fqq5wo9nsej30ge07sdfy.jpg)

# when to use __call__

解释了是什么和如何用,最后就到重点了,什么时候去用? 其实关于 __call__ 的使用,很多框架都有所涉及,最常见的就是 Flask 和 Django中关于表单验证中的使用,比如说 DataRequired, Length, InputRequired,EqualTo 等等,从这些我们不难看出:

1.  当你的对象装饰、模拟或抽象函数的概念,这个时候你就可以考虑使用 __call__ 这个魔术方法了。
2.  当然我觉得当你的类有一个比较基础的操作,但是想不到好的命名的时候,使用 __call__ 也不失为一个好办法，比如:用 run() , doSth() 这些命名的时候

stackoverflow 上还提到了另外的一些使用情况,有兴趣可以点击链接 [When is using __call__ a good idea?
](https://stackoverflow.com/questions/3369640/when-is-using-call-a-good-idea) 进一步去了解。除此之外 SegmentFault 上也有相关问题可以一看 [Python2.7总的__call__方法在实际环境中有什么实用的地方?](https://segmentfault.com/q/1010000006113393?_ea=1020343)

# References

[ 1 ] [object.__call__(self[, args...])](https://docs.python.org/3/reference/datamodel.html#object.__call__)