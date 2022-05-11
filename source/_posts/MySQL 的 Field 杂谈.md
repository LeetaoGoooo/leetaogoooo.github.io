---
title: MySQL 的 Field 杂谈
tags: ['MySQL']
date: 2022-05-11
categories: ['MySQL']
comments: true
---

# 前言
`Field` 是 MySQL 中的一个函数，其基本使用方法如下：
**作用**：
用于返回指定值在给定值列表中的索引位置
**语法**：
``` mysql
FIELD(value, val1, val2, val3, ...)
```
**例子**：
``` mysl
SELECT FIELD("q", "s", "q", "l");
```
上面的 sql 就是返回字母 `q` 在 `s,q,l` 中的位置，这里和编程语言中不一样，返回的下标是从 1 开始的，所以上述的返回结果是 `2`

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141356.png)

如果查找的字符不在列表里呢？
```
SELECT field("a","s", "q", "l");
```
返回为 0 ，结果如下：

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141448.png)

# 应用场景
说完了，基本用法，说一下 Field 的应用场景，单纯从函数推测出它的应用场景，但是它的实际应用场景，在某种程度上还是很实用的，它可以**自定义排序**。
所谓的自定义排序就是按照我们给定的顺序对数据结果进行排序，通常情况下，我们通过 `order by field` 无外乎就是降序或者升序排列，但是在某些特殊的业务场景下，你可能预期按照自己的规则去定义排序。
## 例子
如下表所示

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141521.png)

默认的情况下，显然是按照 id 进行升序排列的，但是如果需要按照人名 `a,c,b,d`来排序。也就是 `name=a,c,b,d` 来排序。
这个时候就可以借由 `field` 函数去实现了：

``` mysql
SELECT * FROM user ORDER BY FIELD(name, 'a','c','b','d')
```

执行结果如下:

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141543.png)

# 延伸
现在 user 表里新增了三条记录:

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141633.png)

现在有这样子的需求，需要将记录先按照 `x,y,z` 排序，后面按照正常排序即结果如下：

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141654.png)

如何可以做到？
原理其实很简单，归根结底就是 `order by` 和 `field` 的联合应用。首先需要明白，`order by` 的排序原理，`order by` 原理其实很简单，就是**按照我们给定的排序字段（升序或者降序排列）**

## 为什么 'a','c','b','d'

这个时候，再回到最初 user 表只有四条记录时，按照 a,c,b,d 排序

``` mysql
SELECT * FROM user ORDER BY FIELD(name, 'a','c','b','d')
```

这个 sql 发生了什么？

我们用 `Field` 函数构建了一个临时的排序字段，不同的 name 返回在 field 下返回不同的值，相当于构成了一个新的 `sort_field` 字段

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141739.png)

所以这个时候结果就会按照 `a,c,b,d` 的顺序排列了。

## 如何 x,y,z 然后 a,b,c,d

首先如果如果按照 `x,y,z` 在最前面的要求，sql 应当是:

``` mysql
SELECT * FROM user ORDER BY FIELD(name, 'x','y','z')
```
结果如下：

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141829.png)

这个时候的 `sort_field` 表现是这样子的：

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141848.png)

我们预期的应当是

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141907.png)

如果只是将排序由升序改为降序

``` mysql
SELECT * FROM user ORDER BY FIELD(name, 'x','y','z') DESC
```

这个时候结果如下:

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141928.png)

这个结果已经接近 预期了，我们只需要将 `field` 后面的 `x,y,z` 变成 `z,y,x` 就可以了

``` mysql
SELECT * FROM user ORDER BY FIELD(name, 'z','y','x') DESC
```

结果如下:

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/20220511141944.png)
