---
title: MySQL的查询缓存
tags: [MySQL]

date: 2017-07-28
published: true
hideInList: false
feature: 
isTop: false
---







# 什么是查询缓存

MySQL的查询缓存其实就是保存查询返回的完整结果.如果执相同 SQL 语句的时候,并且查询缓存是打开的,则 MySQL 会直接返回结果（在这一过程中 MySQL 还检查了一次用户权限）,跳过解析,优化和执行过程.这里判断 SQL 语句是否相同是通过一个对大小写铭感的哈希查找实现的,这就意味着下面两条查询缓存是不同的:

```mysql
SELECT id FROM t_user;
select id from t_user;
```

简单的了解了一下查询缓存,所以
# 如何查看查询缓存的相关参数
使用下述命令:

```mysql
SHOW VARIABLES LIKE '%query_cache%';  
```

结果:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fhzmb80z47j307603sa9w.jpg)

其中:
1. have_query_cache 查询缓存是否可用
2. query_cache_limit 可缓存具体结果的最大值
3. query_cache_size 查询缓存的大小
4. query_cache_type 阻止或者支持查询缓存

# 如何查看 SQL 查询在缓存中的命中的累积次数
使用下述命令:

```mysql
SHOW STATUS LIKE 'Qcache_hits';
```

# 关于查询缓存

1. 当查询语句中有一些不确定的数据时,则不会被缓存。
2. 如果表发生变化,那么跟这个表相关的所有的缓存数据都将失效,这里的变化包括:INSERT, UPDATE , DELETE, TRUNCATE, ALTER TABLE 等,由此可见对于频繁更新的表，显然是不适合使用查询缓存的.
3. 查询缓存可能可以降低查询时间,但是不能减少查询结果传输的时间消耗,所以是否使用它,需要结合实际情况参考.

对于第一条有如下的简单示例:

```mysql
SELECT COUNT(id) FROM t_user WHERE post_date >= CURDATE(); // 不会被缓存
SELECT COUNT(id) FROM t_user WHERE post_date >= '2017-07-28'; // 会被缓存
```
