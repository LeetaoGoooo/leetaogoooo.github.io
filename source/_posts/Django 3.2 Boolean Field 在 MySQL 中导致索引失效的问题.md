---
title:  Django 3.2 Boolean Field 在 MySQL 中导致索引失效的问题
tags: [python,django,mysql]
date: 2022-11-17
published: true
---

# 前言

公司有个项目使用 Django 开发的，前段时间我们把 Django 的版本升级到最新版本了,然后就出现问题了，我们发现生产环境出现了一个慢 SQL：

```sql
select * from user where not is_delete
```

似乎看着很正常的一个 SQL，唯一需要注意的是这个 **is_delete** 字段，在 `model.py` 中是使用 `BooleanField`


# 问题

上面已经说到了我们使用的字段类似是 `BooleanField`,而我们使用的数据库是 MySQL 数据库，MySQL 数据库原生字段类型没有 bool 类型，所以 Django 是以 `tinyint` 去实现的，所以问题就来了，当你把一个 `tinyint` 字段当成 `bool` 字段去使用，就会出现 **类型转换** 的问题，这就是索引之所以失效的原因。

那么为什么在之前的版本没有出现呢？

因为在之前的版本，上面的 SQL 在 Django 会是这样：

```sql
select * from user where is_delete = 0
```

在 Django 3.2 版本之后就变成了前言中的那个 SQL 了，也有人对此提出了疑问，但是很遗憾，官方最后并没有修复这个问题。所以后续在其他数据库中使用 `BooleanField` 需要尤其注意⚠️。

# 解决办法

官方没有修复这个问题，那么就需要用户就解决了，那么有没有解决办法呢？答案是有的，我们需要将涉及 `BooleanField` 的 ORM 改成如下：

```python
.filter(bool_field=models.Value(0)) # models.Value(1)
```

# 参考

[Performance regression in Exact lookup on BooleanField on MySQL.](https://code.djangoproject.com/ticket/32691)