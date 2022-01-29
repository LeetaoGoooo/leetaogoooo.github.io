---
title: Install Oracle 11g Express (XE) on CentOS 7
tags: [Oracle]

date: 2016-12-19
published: true
hideInList: false
feature: 
isTop: false
---






This will configure on-boot properties of Oracle Database 11g Express 
Edition.  The following questions will determine whether the database should 
be starting upon system boot, the ports it will use, and the passwords that 
will be used for database accounts.  Press <Enter> to accept the defaults. 
Ctrl-C will abort.

Specify the HTTP port that will be used for Oracle Application Express [8080]:

Specify a port that will be used for the database listener [1521]:

Specify a password to be used for database accounts.  Note that the same
password will be used for SYS and SYSTEM.  Oracle recommends the use of 
different passwords for each database account.  This can be done after 
initial configuration:
Password can't be null. Enter password:
Confirm the password:

Do you want Oracle Database 11g Express Edition to be started on boot (y/n) [y]:y

Starting Oracle Net Listener...Done
Configuring database...Done
Starting Oracle Database 11g Express Edition instance...Done
Installation completed successfully.
```

安装成功后将会创建一个 /u01 的文件

# 环境配置

安装完成之后为了设置 Oracle 运行所需的环境变量,执行 */u01/app/oracle/product/11.2.0/xe/bin* 下的 *oracle_env.sh* 的脚本

```shell
[root@bogon local]# cd /u01/app/oracle/product/11.2.0/xe/bin/
```

针对当前用户设置环境变量,运行 *. ./oracle_env.sh* 命令

```shell
[root@bogon bin]# . ./oracle_env.sh
```

如果想让其他用户拥有同样的权限,可以将下述命令添加到你需要的用户的 .bashrc 或者 .bash_profile 文件当中去

```shell
. /u01/app/oracle/product/11.2.0/xe/bin/oracle_env.sh  
```

现在我们可以尝试通过命令行访问 *SQL*Plus* 了

```shell
[root@bogon bin]# sqlplus /nolog

SQL*Plus: Release 11.2.0.2.0 Production on 星期日 12月 18 18:51:42 2016

Copyright (c) 1982, 2011, Oracle.  All rights reserved.

SQL> connect sys/Password as sysdba  
Connected.  
SQL> 
```

# 允许远程访问 Oracle 11g XE GUI

为了允许远程访问 Oracle 11g XE GUI, 在 SQL*Plus 执行下列语句

```sql
SQL>  EXEC DBMS_XDB.SETLISTENERLOCALACCESS(FALSE); 
PL/SQL 过程已成功完成。
```

现在你可以访问 Oracle 11g XE GUI 通过该网址 http://localhost:8080/apex/f?p=4950:1
将 locahost 替换为你的ip地址或者域名同样也可以访问,登录使用的密码是配置 Oracle 中设置的密码


