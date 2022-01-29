---
title: CentOS7安装Mysql
tags: [Linux,MySQL]

date: 2016-12-04
published: true
hideInList: false
feature: 
isTop: false
---







# 查看 CentOS 7的 yum 是否有 mysql-server 文件
打开控制台,输入如下命令行:

```shell
yum list | grep mysql
```

注意:确保能上网, YUM 源配置到了 Internet 上,如果 Linux 无法上网则无法正确使用 yum 命令.

# 下载与安装
CentOS 7的yum源中没有正常安装MySQL时的mysql-sever文件,需要去官网上下载,[官网地址](http://dev.mysql.com/downloads/repo/yum/)
然后从下列文件选择其中一个文件下载:
![](http://ww3.sinaimg.cn/large/d9e82fa4jw1f8vis1jxhyj20th06z769.jpg)
然后在文件所在文件下,打开命令行使用如下命令(这里以 mysql57-community-release-el7-9.noarch.rpm 为例):

```shell
rpm -ivh mysql57-community-release-el7-9.noarch.rpm
yum install install mysql-server
```

然后一路 yes 就 ok 了~
安装之后重启 mysql 服务

```shell
service mysqld restart
```

数据库安装成功了,接下来就是配置数据库了.

# 配置数据库
## 获取临时密码
为了加强安全性, MySQL 为 root 用户随机生成了密码,用于初次登录,使用如下命令获取临时密码:

```shell
grep "password" /var/log/mysqld.log
```

![](http://ww4.sinaimg.cn/large/d9e82fa4jw1f8vjrbvjt5j20kb01q3ys.jpg)
然后将密码复制下来~,接着进入数据库:

```shell
mysql -u root -p
```

然后安装命令行提示输入密码,成功进入数据库,然后尝试 *show databases*,你会发现提示你修改密码

## 修改密码
修改密码的命令行如下:

```sql
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('Your new password')
```

注意:如果你的密码设置的过于简单就会出现下列错误:

```sql
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
```

之所以会出现这个问题,跟 **validate_password_policy** 的值有关
validate_password_policy 有以下取值:

```shell
Policy Tests Performed 0 or LOW Length 1 or MEDIUM Length; numeric, lowercase/uppercase, and special characters 2 or STRONG Length; numeric, lowercase/uppercase, and special characters; dictionary file
```

默认是1，即MEDIUM，所以刚开始设置的密码必须符合长度，且必须含有数字，小写或大写字母，特殊字符.老实说平时数据库密码都很简单,方便记忆,所以这里同样为了可以设置简单的密码,需要修改两个全局参数.

### 修改 validate_password_policy 值

```sql
mysql> set global validate_password_policy=0;
Query OK, 0 rows affected (0.00 sec)
```

修改完这个之后,判断密码的标准就基于密码的长度了.这个由 **validate_password_length** 来决定.

### 修改 valitdate_password_length
validate_password_length 参数默认为8,它也有最小值的限制,最小值为:

```shell
validate_password_number_count 
+ validate_password_special_char_count 
+ (2 * validate_password_mixed_case_count)
```

其中，**validate_password_number_count** 指定了密码中数据的长度,**alidate_password_special_char_count** 指定了密码中特殊字符的长度,**validate_password_mixed_case_count** 指定了密码中大小字母的长度,由于这些参数默认值均为1,所以 **validate_password_length** 最小值为4.

```sql
mysql> set global validate_password_length= 4;
Query OK, 0 rows affected (0.00 sec)
```

如果将值设置为 < 4 ,valiadate_password_length 将仍为4.将上述两项设置完成之后,就可以设置一个长度为4位的简单 mysql 密码了.