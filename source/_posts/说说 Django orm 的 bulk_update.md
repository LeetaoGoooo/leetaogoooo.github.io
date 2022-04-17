---
title: Python 列表内存预分配问题
tags: [Python,Django,ORM]
date: 2022-04-12
categories: ['优化']
comments: true
---

> 以下例子以 mysql 为准

# 前言

django orm 中关于更新，有两种操作分别是：
  1. update
  2. bulk_update

对于 `create` 和 `bulk_create` 这俩类，毫无疑问存在大量数据插入的时候，后者效率更高一些。但是对于更新则需要根据实际情况进行分析了，了解 django 是如何实现 `bulk_update` ，对于我们使用会有一定的好处。

`bulk_update` 的应用场景是，每个需要更新的数据的 value 值各不相同，如果是相同，则没有必要去使用 `bulk_update`

<!--more-->

# 源码

对于 `update` ，没什么好说的，就是底层 sql 就是一个常见的 update 语句，那么 `bulk_update` 呢？首先要明确知道，mysql 的 update 并不存在类似 insert 这种 `insert t values (x),(y),...(z)` 语法的，那么 Django 中是如何实现 `bulk_update` 呢？

源码地址：[bulk_update](https://github.com/django/django/blob/c6b4d62fa2c7f73b87f6ae7e8cf1d64ee5312dc5/django/db/models/query.py#L723)

``` python
	      def bulk_update(self, objs, fields, batch_size=None):
	          """
	          Update the given fields in each of the given objects in the database.
	          """
	        	...
	          # PK is used twice in the resulting update query, once in the filter
	          updates = []
	          for batch_objs in batches:
	              update_kwargs = {}
	              for field in fields:
	                  when_statements = []
	                  for obj in batch_objs:
	                      attr = getattr(obj, field.attname)
	                      if not hasattr(attr, "resolve_expression"):
	                          attr = Value(attr, output_field=field)
	                      when_statements.append(When(pk=obj.pk, then=attr))
	                  case_statement = Case(*when_statements, output_field=field)
	                  if requires_casting:
	                      case_statement = Cast(case_statement, output_field=field)
	                  update_kwargs[field.attname] = case_statement
	           ...
```
从源码不难看得出来，django 的批量更新是通过 `case when` 去实现的。

# 性能对比

网上也有对于 case when 和常规 update 的性能对比，这里也不再通过实例对比，直接贴出结论：

## RC隔离级别

更新条数小(一般小于500条)，`CASE WHEN` 优于 `UDPATE`

更新条数较大(千级别)，`CASE WHEN` 效率迅速下降

## RR隔离级别

`CASE WHEN` 优于 `UPDATE`

## RC隔离级别

更新条数小，`CASE WHEN` 优于 `UDPATE`

更新条数较大(千级别)，`CASE WHEN` 效率迅速下
# 结论
所以说，数据过多的情况下，优先使用 `update` ? 理论上是这样子的，但是 django 的 `bulk_update` 中还有一个额外的参数 `batch_size`,通过 `batch_size` 可以限制一次批量更新的数据长度，也就避免了大量数据更新造成的不良影响。

最后需要 bulk_update 的时候，记得使用事务
# 参考链接

[bulk_update](https://docs.djangoproject.com/zh-hans/4.0/ref/models/querysets/#bulk-update)

[Running a 'bulk update' efficiently with Django](https://www.reddit.com/r/django/comments/mjpbij/running_a_bulk_update_efficiently_with_django/)

[Mysql批量更新的三种方式](https://www.cnblogs.com/AaronCui/p/10968893.html)
