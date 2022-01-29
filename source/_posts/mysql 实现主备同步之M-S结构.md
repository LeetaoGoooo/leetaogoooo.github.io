---
title: mysql 实现主备同步之M-S结构
tags: [MySQL]

date: 2021-04-29
published: true
hideInList: false
feature: 
isTop: false
---

# 前言


主备同步，也叫主从复制，是 mysql 提供的一种高可用的解决方案，保证主备数据一致性的解决方案。
在生产环境中，会有很多不可控因素，比如数据库服务器宕机等，因此在生产环境中，都会采用主备同步。在应用的规模不大的情况下，一般会采用一主一备。除此之外，采用主备同步还可以：

- 提高数据库的读并发性，大多数应用都是读比写要多，采用主备同步方案，当使用规模越来越大的时候，可以扩展备库来提升读能力。
- 备份，主备同步可以得到一份实时的完整的备份数据库。
- 快速恢复，当主库出错了（比如误删表），通过备库来快速恢复数据。

那么主备同步的原理是什么？
# 主备同步的实现原理


主备同步模式之所以能够实现，显然是有一种手段可以，将主的 mysql 服务（以下简称 master）执行的 DDL 和 DML 语句传递给 备份的 mysql 服务（以下简称为 slave），这个就是 MySQL 的 **binlog**，mysql 的 binlog 是 MySQL 最重要的日志，它记录了所有的DDL和DML(除了数据查询语句)语句，以事件形式记录，还包含语句所执行的消耗的时间，MySQL的二进制日志是事务安全型的。
所以 binlog 一般会有两个用途：

- 主备模式下，master 将 binlog 传递给 slave，从而达到数据一致的目的
- 数据恢复，可以通过 mysqlbinlog 工具来恢复数据



当 master 将 binlog 传递到 slave 的时候，会被传到 slave 的 **relay log**，relay log 也叫**中继日志**，是连接 master 和 slave 的核心，relay-log 的结构和 binlog 非常相似，只不过他多了一个 master.info 和 relay-log.info 的文件。


master.info 记录了上一次读取到 master 同步过来的 binlog 的位置，以及连接 master 和启动复制必须的所有信息。
relay-log.info 记录了文件复制的进度，下一个事件从什么位置开始，由 sql 线程负责更新。

![数据库同步原理](/posts/1619685348.png "数据库同步原理")

 说完了原理，接下来说说常见的俩种主备模式以及实践吧。
 
# 主备模式与实践
常见的主备模式有俩种分别是 **M-S 结构** 和 **双 M 结构**，本篇文章介绍前者 -- **M-S 结构**

## M-S结构
M-S结构，两个节点，一个当主库、一个当备库，不允许两个节点互换角色。

![M-S结构](/posts/1619685453.png "M-S结构")

在状态1中，客户端的读写都直接访问节点A，而节点B是A的备库，只是将A的更新都同步过来，到本地执行。这样可以保持节点B和A的数据是相同的。
当需要切换的时候，就切成状态2。这时候客户端读写访问的都是节点B，而节点A是B的备库。


### 实践
#### 修改 master 配置
首先修改 master 中的 my.cnf 
```bash
vi /etc/my.cnf
```
在 my.cnf 中 [mysqld] 中添加
```shell
[mysqld]
bind-address=192.168.1.100
server-id=1
binlog-ignore-db = "mysql"
binlog-format = mixed 
log-bin=mysql-bin
datadir=/var/lib/mysql
innodb_flush_log_at_trx_commit=1
sync_binlog=1

#备注：
# server-id 服务器唯一标识。
# log_bin 启动MySQL二进制日志，即数据同步语句，从数据库会一条一条的执行这些语句。
# binlog_do_db 指定记录二进制日志的数据库，即需要复制的数据库名，如果复制多个数据库，重复设置这个选项即可。
# binlog_ignore_db 指定不记录二进制日志的数据库，即不需要复制的数据库名，如果有多个数据库，重复设置这个选项即可。
# 其中需要注意的是,binlog_do_db和binlog_ignore_db为互斥选项，一般只需要一个即可。
```
#### 重启 master 的 mysql
保存之后，然后重启 MySQL
```bash
service restart mysql
```
#### 创建同步帐号
接着以 root 帐号登录 master 的 mysql，创建一个用于同步的帐号，以下建立同步帐号名为 replication,密码为 password, slave 的 ip 是 192.168.1.101
```sql
mysql> CREATE USER replication@192.168.1.101;
mysql> GRANT REPLICATION SLAVE ON *.* TO replication@192.168.1.101 IDENTIFIED BY 'password';
mysql> flush privileges;
mysql> SHOW MASTER STATUS;
mysql> exit;
```
#### 数据初始化
到此关于 master 的配置结束了，**如果这个时候，要同步的数据库有数据，可以将数据 sql 文件，以便于前期的数据初始化**
```shell
mysqldump –skip-lock-tables –all-databases –user=root –password –master-data > master.sql
```
#### 修改 slave 的配置
接下来对 slave 的进行修改，首先修改 my.cnf，将下述内容加到 [mysqld] 区域:
```shell
[mysqld]
server-id=2
binlog-format=mixed
log_bin=mysql-bin
relay-log=mysql-relay-bin
log-slave-updates=1 # 默认是关闭的，这个时候 salve 从 master 复制的数据不会写入到 log-bin 日志文件中，开启后，则会写入
```
#### 重启 slave 的 mysql
然后重启 mysql，重启之后，以 root 用户登录 MySQL, 新建要同步的数据库
```shell
service restart mysql
mysql -uroot -p
mysql> create database database-name;
mysql> exit;
```
#### 导入 master 的数据
将 master 的导出的 sql 文件，加载到 slave 数据中，进行数据初始化
```sql
mysql -u root -p database-name < master.sql
```
#### 设置跟踪主库日志文件
在开启 slave 服务之前，还需要进行一些设置
```sql
mysql> CHANGE MASTER TO 
    -> MASTER_HOST='192.168.1.100',
    -> MASTER_USER='replication',
    -> MASTER_PASSWORD='password', 
    -> MASTER_LOG_FILE='mysql-bin.000001', 
    -> MASTER_LOG_POS=500;
mysql> START SLAVE;
mysql> SHOW SLAVE STATUS \G;
```
每个参数的含义如下:

- master_host 为主库IP地址

- master_user 为主库用户名

- master_password 为主库密码

- master_log_file 为主库日志文件

- master_log_pos 为主库日志所占位置

其中 MASTER_LOG_FILE 及 MASTER_LOG_POS 是在 Master 上在 MySQL 执行 "SHOW MASTER STATUS;"  的结果。
#### 启动 slave 服务,并查看slave服务信息
```sql
mysql> START SLAVE;
mysql> SHOW SLAVE STATUS \G;
```
如果没有错误信息的话，就说明配置成功了。

# 参考资料

[mysql 实现主备同步](https://blog.csdn.net/weixin_36331058/article/details/113334531)

[MySQL中的binlog和relay-log结构完全详解](https://blog.csdn.net/qwe123147369/article/details/108670385)

[MySQL主从复制与双主互备](https://zhuanlan.zhihu.com/p/135465843)

[Mysql log 日志](https://www.yipzale.me/2018/07/03/mysql-log.html)

[mysql中binlog_format模式与配置详解](https://www.cnblogs.com/langtianya/p/5504774.html)

[How To Set Up Master Slave Replication in MySQL](https://www.digitalocean.com/community/tutorials/how-to-set-up-master-slave-replication-in-mysql)






