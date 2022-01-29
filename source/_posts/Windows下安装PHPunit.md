---
title: Windows下安装PHPunit
tags: [PHP]

date: 2016-12-08
published: true
hideInList: false
feature: 
isTop: false
---







# 安装 Pear
可以参考 [Windows下PHP安装PEAR](http://www.leetao94.cn/2016/12/08/Windows%E4%B8%8BPHP%E5%AE%89%E8%A3%85PEAR/)

# 安装 PHPunit
## 准备工作

在命令行下输入以下命令:

```shell
pear list
```

获得如下结果:

![](http://ww1.sinaimg.cn/large/d9e82fa4jw1fajpw03zp9j20at04mq41.jpg)

如果没有以上安装包,请依次安装,安装命令如下:

```shell
pear install xxxx
```

![](http://ww2.sinaimg.cn/large/d9e82fa4jw1fajq0gbxxsj20mk08tgpo.jpg)

## 安装 PHPunit

上述准备工作完成之后,就开始重头戏,安装 PHPunit, 输入如下命令行:

```shell
pear channel-discover pear.phpunit.de
pear install phpunit/PHPUnit
```

很遗憾的是,我通过该方法安装失败了，改用下列方法成功安装

1. 下载 https://phar.phpunit.de/phpunit.phar 并将文件保存到 php.exe 所在文件目录下
2. 在当前目录下打开命令行
3. 建立外包覆批处理脚本

```shell
echo @php "%~dp0phpunit.phar" %* > phpunit.cmd
```

然后测试,是否安装成功:

```
phpunit --version
```

![](http://ww1.sinaimg.cn/large/d9e82fa4jw1fajqwtevf2j20fv01jq35.jpg)

出现上图就说明我们已经大功告成了!

