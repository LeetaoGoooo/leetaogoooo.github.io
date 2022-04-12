---
title: Python 列表内存预分配问题
tags: [Python]
date: 2022-04-12
categories: ['优化']
comments: true
---

> 以下例子均的运行环境为 Python 3.9.5，不同版本的实际运行结果可能有所不同

# 前言

前端时间看了 Golang 的 学习了一下 数组 和 Slice，其中 Slice 相当于动态数组，其中数组的长度是固定的，而 Slice 则是不定长的。在 Python 中是没有数组和 Slice 的概念，它们可以通通归类为 List（列表），那么问题来了，定长的 List 和 不定长的 List 在表现上会有区别吗？（这里的定长的 List 是指对 List 进行初始化,也就是所谓的预分配）
接下来通过几个例子去验证这个问题。

<!--more-->

# 实验过程

首先验证一下 空列表 以及在不断新增元素的情况下 List 的 size 的变化
``` python
	  from sys import getsizeof
	  
	  l = []
	  
	  print(f'空列表:{getsizeof(l)}')
	  
	  for i in range(10):
	      l.append(l)
	      print(f'存在{i+1}元素的列表:{getsizeof(l)}')
```

看一下输出结果

```
	  空列表:56
	  存在1元素的列表:88
	  存在2元素的列表:88
	  存在3元素的列表:88
	  存在4元素的列表:88
	  存在5元素的列表:120
	  存在6元素的列表:120
	  存在7元素的列表:120
	  存在8元素的列表:120
	  存在9元素的列表:184
	  存在10元素的列表:184
```

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/image_1649729308457_0.png)

从上面的运行结果可以发现，其实 List 也存在一定的扩容机制的，列表初始化过程中将会申请一个存储四个元素的存储区，当存储区填满时，列表会再次申请四个存储空间存储元素。当元素量达到原存储空间的两倍时，列表会再次申请原来旧的存储空间的两倍的容量存储元素。

不管任何语言在一旦进行扩容操作必然会存在：
  	1. 重新申请内存
		2. 复制老数组到新的内存
		3. 回收老数组的内存

上面的过程如果出现的次数比较频繁必然会导致运行速度的降低，那么究竟会降低多少呢？

## 例子

以下例子运行环境为 ipython

### 没有任何处理的例子

``` python
	  %%timeit
	  l=[]
	  for i in range(1000):
	      l.append(i)
```

运行结果如下：

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/%E6%88%AA%E5%B1%8F2022-04-12_%E4%B8%8B%E5%8D%882.42.23_1649745745624_0.png)

### 预分配后的列表

``` python
	  %%timeit
	  size = 1000
	  l = [None] * size
	  
	  for i in range(size):
	      l[i] = i
```

和上面唯一不同的是，我们在遍历之前，给列表 l 预先分配了一个长度为 1000 的 None 的空数组，运行结果如下：

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/%E6%88%AA%E5%B1%8F2022-04-12_%E4%B8%8B%E5%8D%882.45.50_1649745952420_0.png)

从这两个例子不难看出，如果预先知道要使用数组的大小，并且对列表进行初始化的话，是可以在一定程度上提高运行效率的。

# 结尾

上面的例子，如果使用列表推导式速度会更快。

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/%E6%88%AA%E5%B1%8F2022-04-12_%E4%B8%8B%E5%8D%882.50.30_1649746232891_0.png)

列表预先配可能并不适合复杂场景，但是一旦有符合这个特殊场景的情况下效果应该是蛮不错的。
