---
title: 普通类方法和classmethod和staticmethod区别
tags: [Python]

date: 2017-07-21
published: true
hideInList: false
feature: 
isTop: false
---







最近写Python的时候,写类方法的时候,编译器老是建议我把一些方法改成静态方法,所以静态方法和普通方法有什么区别呢？当然提到了静态方法,也顺带提一下类方法，其中关于静态方法和类方法的区别，下面一段英文我觉得已经很好的解释了。

>@classmethod means: when this method is called, we pass the class as the first argument instead of the instance of that class (as we normally do with methods). This means you can use the class and its properties inside that method rather than a particular instance.
@staticmethod means: when this method is called, we don't pass an instance of the class to it (as we normally do with methods). This means you can put a function inside a class but you can't access the instance of that class (this is useful when your method does not use the instance).


看完上述的解释,估计会有一些认识了，接下来让我们看一个具体的示例代码

```python

class Test(object):
    test = 1
    def __init__(self,test)
        self.test = test

    def get_test(self):
        print(self.test)
    
    @staticmethod
    def sget_test(test):
        print test
    
    @classmethod
    def cget_test(cls):
        print cls.test

a = Test(2)
a.get_test() #[1]
a.sget_test(3) #[2]
A.sget_test(3) #[3]
Test.cget_test() #[4]

```

其结果如下:

```shell
2
3
3
1
```

上面的代码其实反映了这三种方法的使用场景,普通方法参数自带*self*,这说明，其侧重于对象实体调用，即它有很大程度上需要使用该对象实体特有的某些属性，这里代码指的就是实体*a*初始化传递的参数*2*;而静态方法则不管，对于它来说,无论是对象实体还是类调用,它的表现都一样(见代码[2],[3]处),由于其这种特性常被用来组织类之间有逻辑关系的函数;最后对于类方法来说,很显然它关于的是类本身而不是实体,如果你把一些方法定义成类方法,那么实际上你是希望用类来调用这个方法,而不是用这个类的实例来调用这个方法,示例代码中无论怎么被实例化,其类属性test永远都不会变~~

参考链接:
[Meaning of @classmethod and @staticmethod for beginner?](https://stackoverflow.com/questions/12179271/meaning-of-classmethod-and-staticmethod-for-beginner/12179325#12179325)
[装饰器@staticmethod和@classmethod有什么区别?](https://taizilongxu.gitbooks.io/stackoverflow-about-python/content/14/README.html)
