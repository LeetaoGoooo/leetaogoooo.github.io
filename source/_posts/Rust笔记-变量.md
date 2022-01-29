---
title: Rust笔记-变量
tags: [Rust学习笔记]

date: 2019-01-09
published: true
hideInList: false
feature: 
isTop: false
---






# 变量

变量是所有语言中都不可或缺的重要部分,这一点同样适用 Rust. 但是与其他语言有所不同的是,在 Rust 中变量默认是**不可改变的(immutable)**.

不可改变的就意味着，一旦一个变量赋值之后，我们就不能再次改变它的值了,如果尝试进行修改，则会产生错误.让我们通过一个列子直观的感受一下.

```rust
// example.rs
fn main() {
    let x = 5;
    println!("the value of x is {}",x);
    x = 6;
    println!("The value of x is: {}", x);
}
```
然后用 **rustc example.rs** 编译一下，编译结果如下：

![](http://ww1.sinaimg.cn/large/006wYWbGly1fyzdp19s9bj30ew05l3yy.jpg)


问题来了，实际开发过程中，我们难免会需要对变量重新赋值，那么 Rust 有没有**可变的(mutable)**变量呢？自然是有的，用法很简单，在变量前添加一个关键字 **mut** 就可以了，让我们改造一下上面的例子感受一下

```rust
fn main() {
    let mut x = 5;
    println!("the value of x is {}",x);
    x = 6;
    println!("The value of x is: {}", x);
}
```
先用 **rustc example.rs** 会在当前目录下生成一个可执行文件，然后通过 **./example** 的方式运行编译后的文件,在 windows 下则会生成一个 exe 文件，通过类似的方式运行，结果如下图：

![](http://ww1.sinaimg.cn/large/006wYWbGly1fyzdztabt0j30ex01vweg.jpg)

说道这里，可能大家就会有一个困惑，**默认不可变的变量和常量有什么区别呢？**

# 默认不可变的变量和常量的区别

1. 常量前面不能使用 **mut** 关键字
2. 常量定义使用的关键字是 **const**,并且常量在定义的时候必须声明其类型，eg: **const MAX_POINTS: u32 = 100_000**
3. 常量可以在任意地方声明，包括全局变量
4. 常量只能是一个常数表达式,也就意味着函数调用的结果或者在运行时计算得出的值都不能设置为常量

# 隐藏(Shadowing)

在官方教程中 Rust 在变量中还提及到了一个概念-阴影(Shadowing),所说的内容很简单，就是你通过 **let** 重新声明一个和之前变量名一样的变量，你可以使用之前的变量的值，说起来很抽象，让我们看一下例子:

```rust
fn main() {
    let x = 5;
    println!("the value of x is {}",x);
    let x = x + 6; // x 保留着上一个变量x的值 5
    println!("The value of x is: {}", x);
}
```
然后编译运行一下结果：

![](http://ww1.sinaimg.cn/large/006wYWbGly1fyzesrefotj30dw01zt8n.jpg)

最后再看一个关于 **[变量阴影(Variable shadowing)](https://en.wikipedia.org/wiki/Variable_shadowing)**,个人觉得其实就是变量的作用范围的例子

```rust
fn main() {
    let x = 0;
    
    {
        let x = 1;
        println!("Inner x: {}", x); // prints 1
        
    }
    
    println!("Outer x: {}", x); // prints 0
    
    // Shadow
    let x = "Rust";
    println!("Outer x: {}", x);  // prints 'Rust'
}
```

输出结果:

```bash
Inner x: 1
Outer x: 0
Outer x: Rust
```