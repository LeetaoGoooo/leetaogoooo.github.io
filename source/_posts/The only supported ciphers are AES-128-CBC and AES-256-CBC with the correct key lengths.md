---
title: The only supported ciphers are AES-128-CBC and AES-256-CBC with the correct key lengths
tags: [PHP,Laravel]

date: 2017-06-07
published: true
hideInList: false
feature: 
isTop: false
---







![](http://ww1.sinaimg.cn/large/d9e82fa4ly1fgcuwaft5lj20ul05274j.jpg)

# 问题原因

未设置 Application Key 导致。

# 解决办法

在项目根目录下执行下述命令:

```shell
php artisan key:generate
```
**注意**:在执行该命令执行,需要先将 *.env.example* 复制并重新命名为 *.env*,否则会产生类似的错误信息

```shell
PHP Fatal error:  Uncaught UnexpectedValueException: The stream or file "/var/www/html/ASRM/storage/logs/laravel.log" could not be opened: failed to open stream: Permission denied in /v
ar/www/html/ASRM/vendor/monolog/monolog/src/Monolog/Handler/StreamHandler.php:107
```

执行成功后会返回如下信息:

```shell
lt94@ubuntu:/var/www/html/ASRM$ php artisan key:generate
Application key [base64:yUNJlAGBxWrxRutexftuJzVmzvllHXANxerkojqLscg=] set successfully.
```
然后重新刷新页面,出现下图就说明没有问题了。

![](http://ww1.sinaimg.cn/large/d9e82fa4ly1fgcuwg7hrij20w50edaa7.jpg)