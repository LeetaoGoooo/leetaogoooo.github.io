---
title: 生成器(generator)
tags: [PHP]

date: 2017-06-06
published: true
hideInList: false
feature: 
isTop: false
---







# What is *yield*

*yield* 关键字返回生成器( generator )的数据。

>The heart of a generator function is the yield keyword. In its simplest form, a yield statement looks much like a return statement, except that instead of stopping execution of the function and returning, yield instead provides a value to the code looping over the generator and pauses execution of the generator function.

# What is a generator function

生成器函数是一种更为紧凑和有效编写迭代器的方法。它允许你定义一个函数,在循环时计算和返回结果。

```php
foreach (xrange(1, 10) as $key => $value) {
    echo "$key => $value", PHP_EOL;
}
```

将会有如下的输出:

```php
0 => 1
1 => 2
…
9 => 10
```

你同样可以控制在 *foreach* 中的 $key,通过如下的方式:

```php
yield $someKey => $someValue;
```
在生成器函数中,someKey 是你想要出现的 key,someVaue 则是 val 中的值。在例子中,它则是 i


# What's the difference to normal functions?
你可能会觉得我们用 PHP 原生的 range 函数同样也可以实现一样的输出，这点没错，但是最大的区别是这两种方式的输出是如何得到的。

当我们使用 range 的时候,它在内存中将整个数组创建出来,并将整个数组交给 foreach 去遍历。换句话说，foreach 将自己来操作整个数组。range 函数和 foreach 函数只交互了一次。想想我们接收邮件包裹，派送人员把包裹给你了，然后就离开了，剩下的就需要你自己去打开包裹并发现包裹里装的是什么了。

当我们使用生成函数时，PHP将进入函数并执行它，直到它满足结束或遇到 yield 关键字。 当它遇到 yield 时，它将返回当时值的任何值到外部循环。 然后它回到生成器，并继续从它产生的地方开始。 由于你的 xrange 拥有一个 for 循环，它将执行并产生，直到达到$max。想象一下 foreach 和生成器打乒乓球的场景。

# Why do I need that?

很显然，生成器可以被应用于内存有限的场景中。取决于你的环境而言，如果做一个 range(0,100000)循环可能导致你的脚本严重错误的话，使用生成器仍旧可以很好的运行。或者就如维基百科所言:

>Because generators compute their yielded values only on demand, they are useful for representing sequences that would be expensive or impossible to compute at once. These include e.g. infinite sequences and live data streams.

生成器应该很快。 但请记住，当我们谈论很快时，我们通常是在非常少的场景中讨论。 所以在你现在跑掉并更改所有代码以使用生成器之前，请做一个基准测试，看看它在哪里有意义。

生成器的另一个用例是异步协同程序。 yield关键字不仅返回值，而且还接受它们


# 补充

PHP 的生成器不能满足所有迭代操作的需求，因为如果不查询，生成器永远不知道下一个要迭代的值是什么，在生成器中无法后退或者快进。生成器还是一次性的，无法多次迭代同一个生成器。不过，如果需要的，可以重建或者克隆生成器。

# 待测试
未完待续，通过生成器是否能够解决大文件加载问题，已经是否能够提高读取速度问题。


整理翻译自:
[What does yield mean in PHP?](https://stackoverflow.com/questions/17483806/what-does-yield-mean-in-php)


