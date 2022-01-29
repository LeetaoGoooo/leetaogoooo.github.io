---
title: 由foreach中使用引用导致的问题
tags: [PHP]

date: 2018-02-26
published: true
hideInList: false
feature: 
isTop: false
---







# 问题

看下面一段代码,如果你能够很清楚的知道结果是什么,就不需要阅读本篇文章了.

```php
<?php
$array = [
    'name' => 'php',
    'age' => 123,
];
 
foreach ($array as $key => &$value) {
    echo "key=$key, value=$value" . "</br>";
}

echo "<br>"

foreach ($array as $key => $value) {
    echo "key=$key, value=$value" . "</br>";
}
?>
```

正确输出如下:

```shell
key=name, value=php
key=age, value=123

key=name, value=php
key=age, value=php
```

# 解答

在解释产生上述结果原因之前我们先看下面一段代码

```php
<?php
$array = [
    'name' => 'php',
    'age' => 123,
];
 
foreach ($array as $key => $value) {
    echo "key=$key, value=$value" . "</br>";
}

echo "<br>"

foreach ($array as $key => $value) {
    echo "key=$key, value=$value" . "</br>";
}

?>
```

输出结果就不用说了，下面通过一个图简单了解一下执行的过程

![](http://ww1.sinaimg.cn/large/006wYWbGly1fou2x6n3f7j30i90763yn.jpg)


那么最开始的那段代码执行情况是什么样子的呢?

![](http://ww1.sinaimg.cn/large/006wYWbGly1fou2zcq0f5j30lm0krweq.jpg)

# 参考链接

[php issue with looping over an array twice using foreach and passing value by reference
](https://stackoverflow.com/questions/18669499/php-issue-with-looping-over-an-array-twice-using-foreach-and-passing-value-by-re)

[php issue with looping over an array twice using foreach and passing value by reference
](https://stackoverflow.com/questions/3307409/php-pass-by-reference-in-foreach)