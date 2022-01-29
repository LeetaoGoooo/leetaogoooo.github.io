---
title: PHP比较运算符
tags: [PHP]

date: 2016-10-10
published: true
hideInList: false
feature: 
isTop: false
---






### 使用的PHP版本:5.5.12

PHP中关于 比较运算符有很多个，这里就不一一列举，不过今天在程序中使用了类似下面的代码，然后出现的错误
```php
if($variable1 <= $variable2 <= $variable3){
    //coding ...
}
```
错误信息
```
 Parse error: syntax error, unexpected '<=' (T_IS_SMALLER_OR_EQUAL)
```
然后尝试将中间的比较运算符，换成其他的比较运算符，仍然出现相同错误，最后将代码修改为
```php
if(($variable1 <= $variable2) && ($variable2 <= $variable3)) {
    //coding...
}
```
