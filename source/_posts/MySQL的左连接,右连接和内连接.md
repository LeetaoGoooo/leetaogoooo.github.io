---
title: MySQL的左连接,右连接和内连接
tags: [MySQL]

date: 2016-10-13
published: true
hideInList: false
feature: 
isTop: false
---






# 创建测试表
这里创建两张测试表 
## 创建 test_provinceid 表
```sql
CREATE TABLE `test_province` (
	`id` CHAR(2) NOT NULL,
	`pname` CHAR(6) NOT NULL,
	PRIMARY KEY (`id`)
)
COMMENT='省份'
COLLATE='utf8_general_ci'
ENGINE=InnoDB;
```
## 创建 test_cityid 表
```sql
CREATE TABLE `test_city` (
	`id` CHAR(2) NOT NULL,
	`pid` CHAR(10) NOT NULL,
	`cname` CHAR(10) NOT NULL COLLATE 'latin1_swedish_ci',
	PRIMARY KEY (`id`)
)
COMMENT='城市'
COLLATE='utf8_general_ci'
ENGINE=InnoDB;
```
## 插入测试数据
```sql
INSERT INTO `test`.`test_province` (`id`, `pname`) VALUES ('AH', '安徽省');
INSERT INTO `test`.`test_province` (`id`, `pname`) VALUES ('ZJ', '浙江');
INSERT INTO `test`.`test_province` (`id`, `pname`) VALUES ('HN', '河南');
INSERT INTO `test`.`test_city` (`id`, `pid`, `cname`) VALUES ('01', 'AH', '合肥');
INSERT INTO `test`.`test_city` (`id`, `pid`, `cname`) VALUES ('04', 'AH', '六安');
INSERT INTO `test`.`test_city` (`id`, `pid`, `cname`) VALUES ('02', 'HN', '郑州');
INSERT INTO `test`.`test_city` (`id`, `pid`, `cname`) VALUES ('03', 'HB', '石家庄');
```
数据库中 test_province 数据现在有如下这些:
![](http://ww3.sinaimg.cn/large/d9e82fa4jw1f8qv8m5xg0j203z027t8n.jpg)
数据库中 test_city 数据现在有如下这些:
![](http://ww1.sinaimg.cn/large/d9e82fa4jw1f8qv1x80zbj205902sjrf.jpg)

# 左连接
## 语法
```sql
SELECT *
  FROM a  LEFT JOIN b 
ON a.key = b.key
```
左连接以左边的表作为主表,这里我们以 test_province 为主表,查看一个省份在 test_city 中有哪些城市
## 测试
```sql
select * from test_province a LEFT JOIN (select * from test_city) b on a.id = b.pid
```
## 执行结果
![](http://ww1.sinaimg.cn/large/d9e82fa4jw1f8qveidj0ej209b02q74l.jpg)
注意:这里将省份存在,城市不存在的数据也显示出来了

# 右连接
## 语法
```sql
SELECT *
  FROM a  RIGHT JOIN b 
ON a.key = b.key
```
有上述的左连接的认知,我们不难得出,右连接是以右边的表作为主表的认识,这里我们将上面的语句简单修改一下
## 测试
```sql
select * from test_province a RIGHT JOIN (select * from test_city) b on a.id = b.pid
```
## 执行结果
![](http://ww1.sinaimg.cn/large/d9e82fa4jw1f8qv7cnbavj208w028mxc.jpg)
注意:这里将城市存在,省份不存在的数据也显示出来了

# 内连接
## 语法
```sql
SELECT *
  FROM a  INNER JOIN b 
ON a.key = b.key
```
左连接以左边的表作为主表,有连接以右边的表作为主表,还差一个以双方作为主表的连接,那就是内连接啦,内连接的想法很简单,你有我有大家都有才是真的有,同样简单修改一下语句

## 测试
```sql
select * from test_province a INNER JOIN (select * from test_city) b on a.id = b.pid
```

## 执行结果
![](http://ww3.sinaimg.cn/large/d9e82fa4jw1f8qvcx4pd3j207x02d3yo.jpg)
注意: 这里只显示了城市和省份想匹配的数据