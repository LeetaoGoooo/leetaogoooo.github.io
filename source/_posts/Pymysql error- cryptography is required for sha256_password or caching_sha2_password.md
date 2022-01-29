---
title: Pymysql error- cryptography is required for sha256_password or caching_sha2_password
tags: [MySQL]

date: 2020-04-14
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
重新安装了 Docker，然后启动其中的 MySQL 镜像，通过 pymysql 去连接数据库，出现了下面错误:

>cryptography is required for sha256_password or caching_sha2_password

# 解决方法1

解决方法其实很简答，安装 `cryptography` 这个库就可以解决问题了。

```shell
pip install cryptography
```

# 出现问题的原因

但是为什么会出现这个问题呢？然后查阅了一下 MySQL 的相关资料发现，MySQL8.0会默认使用`caching_sha2_password`作为身份验证插件,这个也就是导致出错的原因。这就意味着，我们也可以通过关闭 `caching_sha2_password` 这个插件解决问题。

# 解决方法2
在MySQL8.0前采用的是 `mysql_native_password` 认证方式，修改步骤如下:

```mysql
$ mysql -u root -p
>use mysql; 选择数据库(这一步不可省略)
>select host, user, plugin from user; // 查看plugin设置
>ALTER USER root@localhost IDENTIFIED WITH mysql_native_password BY 'xxxxx'; // 将 root 用户的认证方式修改为 mysql_native_password
```