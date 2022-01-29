---
title: Mybatis中特殊符号#{}和${}
tags: [Java,mybatis]

date: 2020-09-25
published: true
hideInList: false
feature: 
isTop: false
---



# 前言

一直在 Springboot 中使用 Mybatis，基本上使用到的都是 **#{}**, 由于需求，需要有一个根据视图名可以直接调用视图的函数，一开始 SQL 是这样子:

```sql
SELECT * FROM #{viewName}
```
实际运行过程中传入一个视图名称 viewName 发现实际运行的 SQL 语句是这样子的:
```sql
select * from "viewName";
```
 很明显， SQL 语句存在语法问题，无法执行。最后将 SQL 语句改成:
```sql
SELECT * FROM ${viewName}
```
成功运行，那么 #{} 和 ${} 区别是什么？

# #{} 和 ${}

从上面的例子不难发现俩者最终的在 SQL 语句的表现形式不一样

- \#{} 会使得传入的参数在 SQL 中通常被 "" 包裹；
- ${} 则是参数保持原样。


其实使用 #{} 时, Mybatis 会对其传入的参数进行转义，以达到防止 [SQL 注入攻击](https://baike.baidu.com/item/SQL注入攻击) 的目的，而 ${} 则会保持原样，这就意味着我们需要尽可能的使用 #{} 而不是 ${}。${} 通常会用在传递表名或者字段名的情况下，不过在这种情况下需要自定义校验规则，以防止 SQL 注入攻击.

