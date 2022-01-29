---
title: define和const的区别-PHP常量
tags: [PHP]

date: 2017-07-25
published: true
hideInList: false
feature: 
isTop: false
---







# 什么是常量？
这点官方手册上写的很清楚.
>可以用 define()  函数来定义常量，在 PHP 5.3.0 以后，可以使用 const 关键字在类定义之外定义常量。一个常量一旦被定义，就不能再改变或者取消定义。
常量只能包含标量数据（ boolean ， integer ， float  和 string ）。可以定义 resource  常量，但应尽量避免，因为会造成不可预料的结果。 
可以简单的通过指定其名字来取得常量的值，与变量不同，不应该在常量前面加上 $ 符号。如果常量名是动态的，也可以用函数 constant()  来获取常量的值。用 get_defined_constants()  可以获得所有已定义的常量列表。 

# 常量和变量的区别
这点官方手册也同样提到了.
>1. 常量前面没有美元符号（$）；  
2.  常量只能用 define()  函数定义，而不能通过赋值语句；  
3.  常量可以不用理会变量的作用域而在任何地方定义和访问；  
4.  常量一旦定义就不能被重新定义或者取消定义；  
5.  常量的值只能是标量。 

# 定义常量的方式

```php
<?php
const CONSTANT = "Hello world";
define ( "CONSTANT" ,  "Hello world." );
 ?> 
```

# define 和 const 的区别
两者的根本区别在于*const*在编译期间定义常量,而*define*则是在执行期间。感觉某种程度上*const*有点类似Java中的*final*关键字。由于*const*运行在编译期间,所以就意味着:
***const* 关键字定义常量必须处于最顶端的作用区域**

**不能在函数内，循环内以及 *if* 语句之内用 *const* 来定义常量, *define* 可以**

```php
if (...) {
    const CONSTANT = 'Hello world';    // 错误
}

if (...) {
    define ( "CONSTANT" ,  "Hello world." ); // 正确
}
```
***const* 大小写敏感,*define*可以忽略大小写敏感**
const

```php
const CONSTANT = "Hello world";
echo CONSTANT."\n";
echo Constant;
```
输出

```shell
Hello world

Notice: Use of undefined constant Constant - assumed 'Constant' in C:\Users\Leetao\Desktop\test.php on line 4
Constant
```

define

```php
define ( "CONSTANT" ,  "Hello world.", true );
echo CONSTANT."\n";
echo Constant;
```
输出

```shell
Hello world.
Hello world.
```

***const* 只能接受静态的标量，而 *define* 可以采用任何表达式**

```php
const BIT_5 = 1 << 5;    // [1]
define('BIT_5', 1 << 5);  // [2]
```
其中 [1] 在 PHP 5.6 之后的版本都可以正常使用,[2] 一直都是正确的用法

***const* 采用普通的常量名称，*define*可以采用表达式作为名称**

```php
const  FOO = 'BAR';
for ($i = 0; $i < 32; ++$i) {
define('BIT_' . $i, 1 << $i);
}
```

***const* 可以在当前命名空间定义常量，而*define*则需要补全路径**

```php
namespace A\B\C;
const FOO = 'BAR';
define('A\B\C\FOO', 'BAR');
```

***const*可以在类或者接口中使用,而*define*不可以**

```php
class Foo {
    const BAR = 2; // 正确
}
// but
class Baz {
    define('QUX', 2); // 错误
}
```

**自PHP5.6之后 *const* 可以定义数组常量,而*define*在PHP7.0才可以**

```php
const FOO = [1, 2, 3]; 
define('FOO', [1, 2, 3]); 
```

