---
title: ES6 arrow function and function declaration expressions
tags: [Javascript]

date: 2018-08-06
published: true
hideInList: false
feature: 
isTop: false
---







# What is Arrow function

关于什么是箭头函数,让我们看一下 MDN 给出的定义

>An **arrow function expression** has a shorter syntax than a function expression and does not have its own <code>this</code>, <code>arguments</code>, <code>super</code>, or <code>new.target</code>. These function expressions are best suited for non-method functions, and they cannot be used as constructors.

不难看出上面的定义说出了箭头函数的一些特点:没有自己的this，arguments，super或 new.target,更适用于那些本来需要匿名函数的地方，并且它们不能用作构造函数。也就是意味着,如果在没有使用 <code>this</code>,<code>arguments</code> 或者 <code>new</code> 等前提下,你可以使用箭头函数替换原本的函数声明。


# Are arrow function and funciton declaration equivalen

结合上述的说明，很容易得知,箭头函数和函数声明并不相同。通过几个简单的例子说明这一点:

## this

1.	**箭头函数** 默认指向定义它时,它所处的上下文对象,而不是执行期间的对象

```javascript
    var exampleObj = {
        a: 'test',
        arrowOutputA: () => {
            console.log(this.a);
            console.log(this);
        },
        funcOutputA: function () {
            console.log(this.a);
            console.log(this);
        }
    }

    exampleObj.arrowOutputA();
    exampleObj.funcOutputA();
```

输出结果如下:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fu16qj14wxj30fb05lt8o.jpg)

关于 <code>this</code> 如果不是特别了解可以阅读下述链接 [this](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)

2.	箭头函数 <code>this</code> 不可变. <code>call()</code>,<code>apply()</code>,<code>bind()</code> 这些方法也 无法改变 箭头函数 <code>this</code> 的指向

让我们通过一组代码对照一下:

```javascript
window.name = 'window';

var exampeObj = {
	name: 'example'
}

function showName() {
	console.log(this.name)
}

showName.call(exampeObj)
```
输出结果如下:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fu1ew99prrj306p04xaa4.jpg)

从上图我们可以看到,上述代码的 <code>this</code> 指向由从 <code>window</code> 变成了 <code>exampleObj</code>
kan chu
```javascript
window.name = 'window';

var exampleObj = {
	name: 'example'
}

var showName = () => {
	console.log(this.name)
}

showName.call(exampleObj);
```

输出的结果如下:

![](https://ws1.sinaimg.cn/large/006wYWbGly1fu1h993a8wj306804vdfw.jpg)

从上述结果我们不难看出,箭头函数的 <code>this</code> 指向没有发生任何改变

## arguments

上述内容提到箭头函数无法绑定 <code>arguments</code> 参数对象,因此不能通过arguments对象访问传入参数，但是可以用 <code>rest</code> 参数实现。关于 rest 参数具体内容可以访问 [Rest parameters](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Rest_parameters)去进一步了解。

```javascript
var example = (...params) => {
	console.log(params);
}
example(1,2,3);
```

结果如下:

![](https://ws1.sinaimg.cn/large/006wYWbGly1fu1hs0sg5ej307e025a9z.jpg)

## new 

箭头函数内部并没有 <code>Construct</code> 方法，所以没有原型属性（prototype),想开头说的那样没法当构造函数,所以很显然也不能用 <code>new</code> 关键字来实例化对象。

```javascript
var exampleNew = (x) => {this.name = x};
var example = new exampleNew();
```

结果如下图:

![](https://ws1.sinaimg.cn/large/006wYWbGly1fu1hxp6bmdj30bk01saa2.jpg)


# Compatibility

最后看一下箭头函数的兼容性:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fu18ws4mtxj327n0t2whp.jpg)


# References

[What's the meaning of “=>” (an arrow formed from equals & greater than) in JavaScript?](https://stackoverflow.com/questions/24900875/whats-the-meaning-of-an-arrow-formed-from-equals-greater-than-in-javas)

[Arrow function vs function declaration / expressions: Are they equivalent / exchangeable?](https://stackoverflow.com/questions/34361379/arrow-function-vs-function-declaration-expressions-are-they-equivalent-exch)

