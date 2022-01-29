---
title: PHP编码规范(一)基本代码规范
tags: [PHP]

date: 2016-10-24
published: true
hideInList: false
feature: 
isTop: false
---







此处的“类”指代所有的类、接口以及可复用代码块（traits）

### 4.1. 常量

类的常量中所有字母都**必须**大写，词间以下划线分隔。
参照以下代码：

```php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
```

### 4.2. 属性

类的属性命名可以遵循 大写开头的驼峰式 (`$StudlyCaps`)、小写开头的驼峰式 (`$camelCase`) 又或者是 下划线分隔式 (`$under_score`)，本规范不做强制要求，但无论遵循哪种命名方式，都**应该**在一定的范围内保持一致。这个范围可以是整个团队、整个包、整个类或整个方法。

### 4.3. 方法

方法名称**必须**符合 `camelCase()` 式的小写开头驼峰命名规范。

