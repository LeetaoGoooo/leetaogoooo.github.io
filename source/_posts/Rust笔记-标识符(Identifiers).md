---
title: Rust笔记-标识符(Identifiers)
tags: [Rust学习笔记]

date: 2019-01-08
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

和其他语言一样 Rust 同样有 **变量,函数,结构体**等其他概念,因此也需要 **标识符**, 那么关于 Rust 的标识符命名规则和其他语言一样吗?

# 命名规则

1. 和所有语言一样, Rust 同样不能使用关键字 (keyword) 不能作为标识符
2. 当首字符是字母的时候:
    1. 余下的字符可以是字母或者下划线( _ )
3.  当首字符是下划线 ( _ ) 时候:
    1. 标识符长度必须不能小于两字符
    2. 余下的字符可以是字母或者下划线( _ )


相较于其他语言, Rust 中并**不允许数字**作为标识符的一部分.除此之外, Rust 还有一个 **Raw identifiers** 的概念.

# Raw identifier

有的时候你不得不使用 **关键词(keyword)**,比如,当你需要调用一个 c 语言库,碰巧它有个函数名称叫做 ***match***, 为了正常调用这个时候,我们就需要用到 **raw identifier** 了. 使用方法很简单,在函数的开头加上 **r#** 符号即可.

可以看一下官网给的例子:


```rust
let r#fn = "this variable is named 'fn' even though that's a keyword"; 
// call a function named 'match' 
r#match();
```

# 参考链接

[Identifiers](https://doc.rust-lang.org/book/ch03-00-common-programming-concepts.html#identifiers)