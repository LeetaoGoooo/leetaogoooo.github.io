---
title: Rust笔记-数据类型（一）-- 标量
tags: [Rust学习笔记]

date: 2019-01-21
published: true
hideInList: false
feature: 
isTop: false
---






# 数据类型

在Rust中每个值都有自己确定的类型，接下来我们会介绍两类数据类型子集：**标量（scalar）**和 **复合 （compound）**

Rust是静态类型语言，也就意味着在编译期它必须知道所有变量的类型，正常情况下编译器可以根据变量的值推断出变量的类型，也存在没有办法推断的情况，这个时候我们就必须声明这个变量的类型。看一个简单的例子：

```rust
#![allow(unused_variables)]
fn main() {
let guess: u32 = "42".parse().expect("Not a number!");
}
```

这里我们将字符串42转换成数字的时候必须声明转化后的类型，否则的话编译的时候会出错。我们尝试把 u32 去掉编译，结果如下：

```
error[E0282]: type annotations needed
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Not a number!");
  |         ^^^^^
  |         |
  |         cannot infer type for `_`
  |         consider giving `guess` a type
```

最上面我们说道，当编译器无法推断出变量的类型的时候就需要我们自己声明变量类型，这就说明 parse 这个函数返回类型不确定，那么 parse 返回的类型有哪些呢？看一下官方文档

> parse can parse any type that implements the FromStr trait.

那么有哪些类型实现了 FromStr, 简单截部分结果:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fz0m169c0yj309r0b0dg7.jpg)

说完了这些，让我们时候看一下两类数据类型子集了

# 标量类型

Rust 有四个基本的标量类型: 整型(integer),浮点型(floating-point),布尔型(Boolean)，以及字符类型(character)

## 整型

对于整型，学过其他语言的肯定不会模型，也就是整数类型，说明其是没有小数部分的数字。那么 Rust 中的整型有哪些呢？

![](http://ww1.sinaimg.cn/large/006wYWbGly1fz6caie08aj307x069wej.jpg)

**Signed** 和 **Unsigned** 分别代表着**有符号**和**无符号**,即数字是否能为负值. 一个有符号的变体可以存储 -2^(n-1) 到 2^(n-1) - 1, n 代表字节长度，比如 i8的取值范围在 -2^7 到 2^7 - 1. 而无符号的变体可以储存从 0 到 2^n - 1 的数字,则 u8 的取值范围在 0 到 2^8 - 1.

最后说一说，isize 和 usize 类型，它取决于运行程序的计算机架构:64位的
则它们是64位的，32位的则是32位的，通常它们主要用来作为某些集合的索引.

除了常见的10进制，Rust 同样也支持 16进制,8进制，二进制以及 byte 类型(仅支持 u8),Rust 允许在数字后面使用类型后缀，eg: 57u8, 同样也允许使用 **_** 作为分隔符，方便读数,eg: 1_000. 此外 Rust  默认的类型是 i32,通常情况下它的速度会是最快的.

关于整型，Rust  里面还有一个有趣的问题 -- 整型溢出(Integer Overflow )

# 整型溢出 (Integer Overflow)

“整型溢出” 其实很简单，以 u8 为例，它的取值范围为 0-255，如果你把它变成了 256，(**这里的变成是指通过算术运算：+,-,*,/ 等等**)这就叫 “整型溢出”，对于这个 Rust 有一个有趣的规则：当在 debug 模式编译时，Rust 检查这类问题并使程序 panic（这个术语被 Rust 用来表明程序因错误而退出，在 release 构建中，Rust 不检测溢出，相反会进行一种被称为 “two’s complement wrapping” 的操作。简而言之，256 变成 0，257 变成 1，依此类推。

让我们通过例子直观感受一下：

```rust
# 这个项目是通过 cargo 构建的
fn main() {
    let x : u8 = 255;
    let x: u8 = x + 1;
    println!("{}",x);
}
```
看一下编译结果:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fz8o9hyw9xj30ha052gm9.jpg)

说完了整型，就得说一说 Rust 的浮点型了

# 浮点型

Rust 有两个原生的浮点类型:f32,f64，默认是 f64，因为两者速度相当，但是后者精度更高，采用 [IEEE-754](https://baike.baidu.com/item/IEEE%20754/3869922?fr=aladdin
) 标准表示。f32 是单精度浮点数，f64 是双精度浮点数。深入理解可以阅读下面的链接：

# 布尔型

和其他编程语言一样，Rust 的布尔类型也有两个值:true 和 false. 其中布尔类型用 bool 表示.

```rust
fn main() {
    let t = true;

    let f: bool = false; 
}
```
# 字符类型

说完了数字，我们说一说 Rust 的字符， Rust 的 char 类型是语言中最原生的字母类型，唯一需要注意的是：**char 由单引号指定，不同于字符串使用双引号**,看一个具体的例子:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fzegq9txl2j30as030glh.jpg)

值得注意的是,Rust 的 char 类型代表了一个 Unicode 标量值（Unicode Scalar Value），这意味着它可以比 ASCII 表示更多内容,拼音字母（Accented letters），中文、日文、韩文等字符，emoji（绘文字）以及零长度的空白字符都是有效的 char 值。
