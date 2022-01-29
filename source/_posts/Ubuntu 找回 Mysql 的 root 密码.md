---
title: Ubuntu 找回 Mysql 的 root 密码
tags: [Linux,MySQL]

date: 2018-10-10
published: true
hideInList: false
feature: 
isTop: false
---







# 前言

年纪大了就容易忘事,一转眼就把自己的服务器上 MySQL 的 root 密码给忘记了,几经周折最后把密码找回来了,准确的说是把密码重新设置了一下。

# 解决办法

Mysql 版本 5.7.23

## 修改 mysql 的配置文件

在命令下输入如下命令:

```shell
sudo nano /etc/mysql/mysql.cnf
```

然后添加如下配置,网上都说在 <code>[mysqld]</code> 下面添加,我的配置文件什么都没有,我只好手动加了:

```
[mysqld]
skip-grant-tables
```

上面那个参数的意思是:在启动 mysql 时不启动 grant-tables (授权表),不启用权限表会发生什么呢?接着看下去

## 重启 Mysql

修改完配置文件,为了让配置文件生效,自然需要我们重启一下 mysql 服务了

## 进入 Mysql

what? 不是忘记密码了吗？怎么进入 mysql ? 不要急,在命令行上输入下述命令:

```shell
mysql
```

然后回车,你会发现你竟然进入到熟悉的 mysql 界面了,这就是上面那个参数的作用,成功进入界面,接下来就该重设密码了。

## 重设 root 密码

按照下述命令,重设 root 密码

```mysql
>use mysql;
>update user set authentication_string=password('new_password') WHERE User='root';
>exit;
```

最后不要忘记把配置文件的配置注释掉,然后重启 Mysql。
