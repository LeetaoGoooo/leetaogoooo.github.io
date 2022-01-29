---
title: Trait(性状)
tags: [PHP]

date: 2017-06-02
published: true
hideInList: false
feature: 
isTop: false
---







最近想重构一下自己的代码,仔细查看了自己写的代码，发现自己写的两个类,其中一个类是对 Gis 信息的处理展示,另外一个类是生成符合 Highcharts 格式的数据，两个类的功能十分不同，而且在 **继承层次结构** 中没有 **共同的父类**。但是两个类在生成数据之前,都必须对前台传来的指标名称进行转换成相应的指标 id,要怎么解决这个问题呢?

1. 创建一个父类让这两个类都继承
2. 创建一个接口，定义实现转换指标的的方法，然后让这两个类都实现它
3. 创建一个性状，定义实现指标转换的方法，然后让这两个类混入这个性状

第一种解决方法不好，因为这样子做会强制让两个无关的类继承同一个祖先，而且这个祖先不属于各自的继承层次结。
第二种解决方法好，因为每个类都能保有自然的继承层次结构。但是，我们要在两个类中重复实现的地理编码功能，这不符合 DRY (*Don't Repeat Yourself* ) 原则。
第三种解决方法最好，这么做不会搅乱这两个类原本自然的继承层次结构。

>自 PHP 5.4.0 起，PHP 实现了一种代码复用的方法，称为 trait。
Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制。Trait 为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用 method。Trait 和 Class 组合的语义定义了一种减少复杂性的方式，避免传统多继承和 Mixin 类相关典型问题。
Trait 和 Class 相似，但仅仅旨在用细粒度和一致的方式来组合功能。 无法通过 trait 自身来实例化。它为传统继承增加了水平特性的组合；也就是说，**应用的几个 Class 之间不需要继承**。

性状有两个作用:
1. 表明类可以做什么( 像是接口 )
2. 提供模块化实现 ( 像是类 )

性状能把模块化的实现方法注入到多个无关的类中。而且性状还能促进代码重用。

# 性状的创建

```php
trait KeyHandle
{
    public function tranKeyNameToKeyId($keyname)
    {
        // 将 keyname => keyid
    }
}
```

# 性状的使用

```php
class Gis
{
    use KeyHandle;
    // 类的具体实现
    public funcition getGisByKeyId($keyid)
    {
        
    }
}
```

# 性状的优先级

从基类继承的成员会被 trait 插入的成员所覆盖。优先顺序是来自当前类的成员覆盖了trait的方法,而 trait 则覆盖了被继承的方法。

# 例子

## 可运行示例

### trait 则覆盖了被继承的方法

```php
class  Base  {
    public function  sayHello () {
        echo  'Hello ' ;
    }
}

trait  SayWorld  {
    public function  sayHello () {
         parent :: sayHello ();
        echo  'World!' ;
    }
}

class  MyHelloWorld  extends  Base  {
    use  SayWorld ;
}

 $o  = new  MyHelloWorld ();
 $o -> sayHello ();
```
输出:

```shell
Hello World!
```

### 当前类的成员覆盖了 trait 的方法

```php
<?php
trait HelloWorld {
    public function sayHello() {
        echo 'Hello World!';
    }
}

class TheWorldIsNotEnough {
    use HelloWorld;
    public function sayHello() {
        echo 'Hello Universe!';
    }
}

$o = new TheWorldIsNotEnough();
$o->sayHello();
?>
```
输出:

```shell
Hello Universe!
```

## 项目示例

```php
$gis = new Gis();
$keyid = $gis->tranKeyNameToKeyId($keyname);
return $gis->getGisByKeyId($keyid);
```

