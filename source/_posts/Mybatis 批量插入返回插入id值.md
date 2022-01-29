---
title: Mybatis 批量插入返回插入id值
tags: [Java,mybatis]

date: 2019-04-19
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

在 Mybatis 3.3.1 版本之后, Mybatis 支持批量插入返回自增主键 ID,相关 PR 可以参考 [
Support insert multiple rows and write-back id](https://github.com/mybatis/mybatis-3/pull/547), 但是使用的时候有一些注意事项,我在用的时候查了很多资料,发现网上基本上都是只提到一部分,大部分人都是测试失败了,因此整理了一下.

# 使用

## XML 形式

```xml
<insert id="insertList" useGeneratedKeys="true" keyProperty="id">
  INSERT INTO country (countryname,countrycode )
  VALUES
  <foreach collection="list" item="item" separator=",">
    (#{item.countryname},#{item.countrycode})
  </foreach>
</insert>
```

**注意: keyProperty="id" 和 collection="list"**,这两点不能有任何改变,否则就没办法成功获取插入后的 id

## 注解形式

```java
@Insert("<script>
 INSERT INTO country (countryname,countrycode )
  VALUES
  <foreach collection=\"list\" item=\"item\" separator=\",\">
    (#{item.countryname},#{item.countrycode})
  </foreach>
</script>")
@Options(useGeneratedKeys = true, keyProperty = "id", keyColumn = "id")
int batchInsert((@Param("list") List<Country> countryList);
```

**注意: @Param("list"), collection="list" 以及 keyProperty="id"** 这三点不能有任何改变,否则就没有办法成功

# 参考链接

[Support insert multiple rows and write-back id.More about insert multipl...](https://github.com/mybatis/mybatis-3/pull/350)
[Support insert multiple rows and write-back id](https://github.com/mybatis/mybatis-3/pull/547)