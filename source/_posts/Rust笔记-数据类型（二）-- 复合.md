---
title: Rust笔记-数据类型（二）-- 复合
tags: [Rust学习笔记]

date: 2020-06-27
published: true
hideInList: false
feature: 
isTop: false
---



`这是自己学习 Rust 的学习笔记，由于 Rust 语言处于初期，变动可能会比较大，文章不一定会及时同步，学习过程中需要仔细甄别`

# 复合类型

复合类型（Compound types）可以将多个值组合成一个类型。Rust 有两个原生的复合类型：元组（tuple）和数组（array）

## 元组


元组是一个将多个其他类型的值组合进一个复合类型的主要方式。我们使用包含在圆括号中的逗号分隔的值列表来创建一个元组。**元组中的每一个位置都有一个类型**，而且这些不同值的类型也不必是相同的。这个例子中使用了可选的类型注解：

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);}
```

**tup** 变量绑定到整个元组上，因为元组是一个单独的复合元素。为了从元组中获取单个值，可以使用模式匹配（pattern matching）来解构（destructure）元组值，像这样：

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}
```
程序首先创建了一个元组并绑定到 tup 变量上。接着使用了 let 和一个模式将 tup 分成了三个不 同的变量， x 、 y 和 z 。这叫做 解构 ( destructuring )，因为它将一个元组拆成了三个部分。最后，程序打印出了 y 的值，也就是 6.4。让我们看一下编译的结果：

```rust
 Compiling learn_rust_by_example v0.1.0 (F:\workspace\rust\learn_rust_by_example)
warning: unused variable: `x`
 --> src\main.rs:4:10
  |
4 |     let (x, y, z) = tup;
  |          ^ help: consider using `_x` instead
  |
  = note: #[warn(unused_variables)] on by default

warning: unused variable: `z`
 --> src\main.rs:4:16
  |
4 |     let (x, y, z) = tup;
  |                ^ help: consider using `_z` instead

    Finished dev [unoptimized + debuginfo] target(s) in 3.74s
     Running `target\debug\learn_rust_by_example.exe`
The value of y is: 6.4
```

这里编译似乎出现了 warning,提示我们有两个没有使用的变量 x,z, 可以使用 ```_x```, 和 ```_z``` 去替代,ok，让我们试着修改一下代码,

```rust
fn main() {
    let tup = (500, 6.4, 1);
    let (_x, y, _z) = tup;
    println!("The value of y is: {}", y);
}
```

然后重新编译一下:

```
   Compiling learn_rust_by_example v0.1.0 (F:\workspace\rust\learn_rust_by_example)
    Finished dev [unoptimized + debuginfo] target(s) in 0.90s
     Running `target\debug\learn_rust_by_example.exe`
The value of y is: 6.4
```

warning 消失了，那么问题来了 ```_variable``` 和 ```variable``` 有什么区别呢？

首先需要明确的一点是**两者都是正常的变量**，两者最大的区别就在于，只是对于 ```_variable``` Rust 的编译器允许它不被使用。对于 Rust 编译器来说，希望正常定义的变量在接下来都可以被使用的，但是有时候会存在，我们部分变量可能只是一个预留值，为了应对将来的情况，或者函数的拓展，这个时候就可以使用 ```_variable``` 从而消除 Rust 的提示。

除了 ```_variable```, Rust 中还存在 ```_``` 的变量，这两者有很大差别，就像我们上面说的那样 ```_variable``` 是正常的变量，意味着 ```_variable``` 只有当离开作用域才会被销毁，但是 ```_``` 不一样，它会被立即销毁，也就意味着 ```_``` 不存在变量绑定的操作

让我们再次试着修改上面的例子

**eg: _variable**
```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (_x, y, _) = tup;

    println!("The value of y is: {}, x is {}", y,_x);
}
```

结果如下：

```rust
   Compiling learn_rust v0.1.0 (/Users/leetao/Workspace/rt/learn_rust)
    Finished dev [unoptimized + debuginfo] target(s) in 2.86s
     Running `target/debug/learn_rust`
The value of y is: 6.4, x is 500
```

**eg:_**

```rust
fn main() {
    let tup = (500, 6.4, 1);
    let (_x, _y, _) = tup;
    println!("The value of  _ is {}", _);
}
```

结果如下：

```rust
   Compiling learn_rust v0.1.0 (/Users/leetao/Workspace/rt/learn_rust)
error: expected expression, found reserved identifier `_`
 --> src/main.rs:4:39
  |
4 |     println!("The value of  _ is {}", _);
  |                                       ^ expected expression

error: aborting due to previous error
```

说完了，这个我们回到最初的元组，元祖除了用模式匹配解构外，也可以使用点号( . )后跟值的索引来直接访问它们

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);
    let first = x.0; // 索引值也是从 0 开始
    let second = x.1;
    let third = x.2; 
    println!("first :{}, second:{}, third: {}",first,second,third);
}
```
看一下编辑结果:

```rust
   Compiling learn_rust v0.1.0 (/Users/leetao/Workspace/rt/learn_rust)
    Finished dev [unoptimized + debuginfo] target(s) in 0.46s
     Running `target/debug/learn_rust`
first :500, second:6.4, third: 1
```

## 数组

另一个包含多个值的方式是数组。与元组不同，**数组中的每个元素的类型必须相同**。Rust 中 的数组与一些其他语言中的数组不同，因为 Rust 中的**数组是固定长度**的:一旦声明，它们的长度不能增长或缩小. **数组是一整块分配在栈上的内存。** 使用索引来访问数组的元素。

```rust
fn main() {
    let a = [1, 2, 3, 4, 5]; // 写法一
    // let a: [i32; 5] = [1, 2, 3, 4, 5]; 写法二 
    let first = a[0];
    let second = a[1]; 
    println!("first:{}, second:{}",first,second);
}
```
结果：

```rust
   Compiling learn_rust v0.1.0 (/Users/leetao/Workspace/rt/learn_rust)
    Finished dev [unoptimized + debuginfo] target(s) in 0.56s
     Running `target/debug/learn_rust`
first:1, second:2
```

结果很简单，现在让我们看一下数组初始化方法，官方文档定义的语法如下：

>**Syntax**
>ArrayType : 
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[ Type ; Expression ]

正常情况下我们都可以省略掉 **[Type; Expression]**, 因为 Rust 是一个静态类型的语言，这就意味着在编辑期间，编译器可以推断出，我们使用的类型以及数组的长度。

那么现在有一个问题，对于一个数组，我们想限制它的 type, 而不是由编译器在编译期间推断出来，该怎么办呢？

别着急，看我下面的例子，行不行?

```rust
fn main() {
    let a:[i32;_] = [1, 2, 3, 4, 5];
    let first = a[0];
    let second = a[1]; 
    println!("first:{}, second:{}",first,second);
}
```
这里我用上午提到的 ```_``` 替代了原来属于Expression 的位置，是不是看起来没有问题？让我们编译一下试试

```rust
   Compiling learn_rust v0.1.0 (/Users/leetao/Workspace/rt/learn_rust)
error: expected expression, found reserved identifier `_`
 --> src/main.rs:2:16
  |
2 |     let a:[i32;_] = [1, 2, 3, 4, 5];
  |         -      ^ expected expression
  |         |
  |         while parsing the type for `a`

error: aborting due to previous error
```

好像编译失败了，这是为什么呢？


**注意**： `_` 只能在两种情况下使用

    1. 在模式匹配中，用于匹配一个被忽略的值
    2. 作为一个类型的占位符
    
在上面的例子，数组长度是一个 Expression，并不是一个 Type，所以在这里不能使用 `_`，那有没有解决办法呢？自然是有的，你只需要将 Type 添加到数组中任意一个元素上就可以了

```rust
fn main() {
    let a = [1i32, 2, 3, 4, 5];
    let first = a[0];
    let second = a[1]; 
    println!("first:{}, second:{}",first,second);
}
```
看一下结果

```rust
   Compiling learn_rust v0.1.0 (/Users/leetao/Workspace/rt/learn_rust)
    Finished dev [unoptimized + debuginfo] target(s) in 5.09s
     Running `target/debug/learn_rust`
first:1, second:2
```

最后，我们现在都知道数组是通过索引访问的，那么我们出现越界操作会出现什么呢？下面看一个例子：

```rust
fn main() {
    let a = [1, 2, 3, 4, 5]; 
    let index = 10;
    let element = a[index];
    println!("The value of element is: {}", element);
}
```

看一下结果：

```rust
   Compiling learn_rust v0.1.0 (/Users/leetao/Workspace/rt/learn_rust)
    Finished dev [unoptimized + debuginfo] target(s) in 0.07s
     Running `target/debug/learn_rust`
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is 10', src/main.rs:4:19
note: Run with `RUST_BACKTRACE=1` environment variable to display a backtrace.
```

编译并没有产生任何错误，不过程序会出现一个 **运行时(runtime)错误** 并且不会成功退出。当尝试用 索引访问一个元素时，Rust 会检查指定的索引是否小于数组的长度。如果索引超出了数组长度，Rust 会 panic ，这是 Rust 术语，它用于程序因为错误而退出的情况。