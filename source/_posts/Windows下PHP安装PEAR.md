---
title: Windows下PHP安装PEAR
tags: [PHP]

date: 2016-12-08
published: true
hideInList: false
feature: 
isTop: false
---







# 下载 **go-pear.phar** 文件

![](http://ww1.sinaimg.cn/large/d9e82fa4jw1fajpci3mqej20qn0503zj.jpg)

可以直接访问 http://pear.php.net/go-pear.phar 网站，然后右键保存为 go-pear.phar 文件,将该文件保存在 php.exe 所在文件目录下

# 安装 pear

运行如下命令:(在此之前确保, php 已经配置到环境变量当中)

**以管理员身份运行**

```php
php go-pear.phar
```

然后提示安装完成

![](http://ww2.sinaimg.cn/large/d9e82fa4jw1fajpk0ojspj20of0efqad.jpg)

# 导入注册表

安装完成之后,最后控制台会输出下述内容:

```
* WINDOWS ENVIRONMENT VARIABLES *
For convenience, a REG file is available under E:\wamp\bin\php\php5.6.19PEAR_ENV.reg .
This file creates ENV variables for the current user.

Double-click this file to add it to the current user registry.
```

上述内容的意思,在当前目录下有一个名为*PEAR_ENV.reg*的注册表,双击导入注册表，到此为止，安装完成