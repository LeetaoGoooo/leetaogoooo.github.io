---
title: Oracle Database 12c Installation on CentOS 7
tags: [Oracle]

date: 2016-12-22
published: true
hideInList: false
feature: 
isTop: false
---





:|
| Oracle base  | /u01/app/oracle | 
|  Software location    |   /u01/app/oracle/product/12.1.0/dbhome_1 |
| Database file location     |    /u02 |
| Global database name | orcl |

另外请设置合适的 Database edition（数据库版本）及 Character set（字符集）。请为数据库的管理订立一个安全的口令，最后请取消勾选 Create as Container database 项目

**口令**:密码由数字和字母组成,必须包含一个大写字母和一个小写字母
**字符集**:Unicode(AL32UTF8)

![](http://ww3.sinaimg.cn/large/d9e82fa4jw1fayixxs23xj20mb0hrade.jpg)

> 第五步 —— 创建库存

安装缺省的设置 /u01/app/oraInventory 并按 Next。

![](http://ww1.sinaimg.cn/large/d9e82fa4jw1fazb19p2b9j20ma0hrwhm.jpg)

> 第六步 —— 检查先决条件

安装程序会自动检查所有必须的操作系统组件及内核设置。

> 第七步 —— 摘要

这是配置安装的最后机会,请按 Install。

![](http://ww1.sinaimg.cn/large/d9e82fa4jw1fazb8q5fr0j20mb0hrdk3.jpg)

> 第八步 —— 安装

![](http://ww1.sinaimg.cn/large/d9e82fa4jw1fazbxepb3jj20m50hrtbx.jpg)

当询问窗口出现时，请登录成为 root 并执行两个脚本：

![](http://ww4.sinaimg.cn/large/d9e82fa4jw1fazc7e2v94j20f30bpwfv.jpg)

```shell
[root@bogon lt94]# /u01/app/oraInventory/orainstRoot.sh
Changing permissions of /u01/app/oraInventory.
Adding read,write permissions for group.
Removing read,write,execute permissions for world.
Changing groupname of /u01/app/oraInventory to oinstall.
The execution of the script is complete.
```

```shell
[root@bogon lt94]# /u01/app/oracle/product/12.1.0/dbhome_2/root.sh
Performing root user operation.
The following environment variables are set as:
    ORACLE_OWNER= oracle
    ORACLE_HOME=  /u01/app/oracle/product/12.1.0/dbhome_2
Enter the full pathname of the local bin directory: [/usr/local/bin]: <PRESS ENTER>
   Copying dbhome to /usr/local/bin ...
   Copying oraenv to /usr/local/bin ...
   Copying coraenv to /usr/local/bin ...
Creating /etc/oratab file...
Entries will be added to the /etc/oratab file as needed by
Database Configuration Assistant when a database is created
Finished running generic part of root script.
Now product-specific root actions will be performed.
You can follow the installation in a separated window.
```

这两个脚本都必须以 root 的身份来执行,然后出现一个进度条,等待安装完成

![](http://ww2.sinaimg.cn/large/d9e82fa4jw1fazcaw8touj20h10c7ab7.jpg)

>第九步 —— 顺利完成安装

最后一个画面将会通知你安装已经完成并显示 Oracle 企业级管理员的 URL。

![](http://ww2.sinaimg.cn/large/d9e82fa4jw1fazcr0uoa9j20fr0auac2.jpg)

https://localhost:5500/em

请按 OK 来关闭安装程序。

# 安装完成后的任务

## 防火墙

请登录成为 root 并检查已引导的本地

```shell
[root@bogon lt94]# firewall-cmd --get-active-zones
public
  interfaces: eno16777736
```

打开相关的端口

```shell
[root@bogon lt94]# firewall-cmd --zone=public --add-port=1521/tcp --add-port=5500/tcp --add-port=5520/tcp --add-port=3938/tcp --permanent
success
```

```shell
[root@bogon lt94]# firewall-cmd --reload
success
```

```shell
[root@bogon lt94]# firewall-cmd --list-ports
1521/tcp 3938/tcp 5500/tcp 5520/tcp
```

## Oracle 工作环境

请登录为 oracle 用户并在 */home/oracle/.bash_profile* 内加入下列数值

```shell
[root@bogon lt94]# su oracle
[oracle@bogon lt94]$ vim /home/oracle/.bash_profile
```

```shell
TMPDIR=$TMP; export TMPDIR
ORACLE_BASE=/u01/app/oracle; export ORACLE_BASE
ORACLE_HOME=$ORACLE_BASE/product/12.1.0/dbhome_2; export ORACLE_HOME
ORACLE_SID=orcl; export ORACLE_SID
PATH=$ORACLE_HOME/bin:$PATH; export PATH
LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib:/usr/lib64; export LD_LIBRARY_PATH
CLASSPATH=$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib; export CLASSPATH
```

重新加载 bash_profile 使其生效

```shell
[oracle@bogon lt94]$ . /home/oracle/.bash_profile 
```

## 登录数据库

最后请登录数据库：

```shell
[oracle@bogon lt94]$ sqlplus system@orcl

SQL*Plus: Release 12.1.0.2.0 Production on Wed Dec 21 18:28:20 2016

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

Enter password: 
Last Successful login time: Wed Dec 21 2016 18:25:24 -08:00

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> 
```

请利用 Oracle 企业级管理员来管理数据库：
https://<主机名称>:5500/em

