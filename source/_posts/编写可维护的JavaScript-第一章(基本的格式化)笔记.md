---
title: 编写可维护的JavaScript-第一章(基本的格式化)笔记
tags: [Javascript,读书笔记]

date: 2016-12-01
published: true
hideInList: false
feature: 
isTop: false
---







# 缩进层级

保持一定的缩进,使用制表符进行缩进或者使用空格进行缩进.

# 语句结尾

每行代码结尾使用分号.

```javascript
//合法代码
var name = "Nicholas";
function sayName() {
    alert(name);
}

//合法代码,不推荐写法
var name = "Nicholas";
function sayName() {
    alert(name)
}
```
上述两种代码合法的原因有赖于分析器的自动分号插入(Automatic Semicolon Insertion, ASI) 机制,JavaScript 代码省略分号也是可以的.但是存在由于该机制导致的错误.

```javascript
//原始代码
function getData() {
    return 
    {
        "name": "leetao",
        "age": "unknown"
    }
}
//分析器会将它理解成
funcition getData() {
    return;
    {
        "name": "leetao",
        "age": "unknown"
    };
}
//通过将左括号移至于return同一行修复该问题
function getData() {
    return {
        "name": "leetao",
        "age": "unknown"
    }
}
```

# 行的长度

每行的保持一定的长度,推荐80个字符.

# 换行

当每行到达单行最大胆字符数字数限制时,在运算符后换行,下一行需要增加那个层级的缩进.

```javascript
// 好的做法: 在运算符后换行,第二行追加两个缩进
callAFunction(document, element, windoow, "some string value", true, 123,
        navigator);
        
// 不好的做法: 第二行只有一个缩进
callAFunction(document, element, windoow, "some string value", true, 123,
    navigator);
    
// 不好的做法: 在运算符前换行
callAFunction(document, element, windoow, "some string value", true, 123
        , navigator);
```
 
总是将一个运算符置于行尾, ASI 不会自作主张地插入分号.对于语句同样遵循上述规则.不过对于语句,主体部分换行后未一个缩进.该规则存在一个特例: **当给变量赋值时,第二行位置应当和赋值运算符的位置保持对齐.**
 
 
# 空行

建议在下述位置添加空行:

 1. 在方法之间.
 2. 在方法中的局部变量和第一条语句之间.
 3. 在多行或单行注释之前.
 4. 在方法内的逻辑片段之间插入空行,提高可读性.
 

# 命名

1. 变量名遵循驼峰大小写命名法 (Camel Case),并且命名前缀应当是**名词**.
2. 函数名遵循驼峰大小写命名法 (Camel Case),并且命名前缀应当是**动词**.
3. 常量使用大小写字母和下划线来命名,下划线用以分割单词.
4. 构造函数的命名遵循大驼峰命名法 (Pascal Case) ,即以大写字母开始.

# 直接量

## 字符串

1. 多行字符串,使用字符串连接符 (+) 将字符串分成多份.
2. 字符串用单行引号括起来均可,但是一个项目应当保持一种风格.

## 数字

为了避免歧义,不要省略小数点之前或者之后的数字.

## null

建议使用 null 的场景:

1. 用来初始化一个变量,该变量可能赋值为一个对象.
2. 用来和一个已经初始化的变量比较,该变量是不是对象均可.
3. 当函数的参数期望是对象时,用作参数传入.
4. 当函数的返回值期望是对象时,用作返回值传出.

下述场景不应当使用 null:

1. 不要使用 null 来检测是否传入某个参数.
2. 不要用 null 来检测一个未初始化的变量.

## undefined

不管是值是 undefined 的变量还是未声明的变量， typeof 运算结果都是 "undefined".通过禁止使用特殊值 undefined,可以有效地确保只在一种情况下 typeof 才会返回 "undefined": 当变量未声明时.如果你使用了一个可能(或者可能不会)赋值为一个对象的变量时,则将其赋值未 null.

# 对象直接量

创建对象使用对象直接量，在直接量中直接写出所有属性,这种方式可以取代先显式创建地创建 Object 的实例然后添加属性的这种做法.

```javascript
// 不好的写法
var book = new Object();
book.title = "title";
bookl.author = "author";
```
 
当定义对象直接量时,常常在第一行包含左花括号,每一个属性的名值对都独占一行,并保持一个缩进.最后右花括号也独占一行.
 
```javascript
 //好的写法
var book = {
    "title": "title",
    "author": "author"
 }
```
# 数组直接量

不赞成显式地使用 Array  构造函数来创建数组

```javascript
// 不好的写法
var colors = new Array("red", "green", "blue");
var numbers = new Array(1, 2, 3, 4);

// 好的写法
var colors = [ "red", "green", "blue" ];
var numbers = [ 1, 2, 3, 4 ];
```
 
 
 
 
 
 
 