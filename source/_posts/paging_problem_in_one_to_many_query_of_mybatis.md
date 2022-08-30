---
title: paging problem in one to many query of mybatis.md
date: 2022-08-29
tags: [java,mybatis]
categories:
comments: true
---

# 前言

mybatis 支持一对多的关系映射，如果需要在这种关系下，使用 pagehelper 的话，需要注意原本的 sql 以及 resultmap 的用法, 否则可能会导致 pagehelper 返回的 total 比预期的数量大。

<!--more-->

# 例子

## mapper

```xml
<resultMap id="authorWithBlogsResult" type="AuthorWithBlogs">
    <id column="id" jdbcType="BIGINT" property="id"/>  
    <result column="name" jdbcType="VARCHAR" property="name"/>

    <collection property="blogs" ofType="Blog">  
        <result column="blog_id" jdbcType="BIGINT" property="id"/>  
        <result column="title" jdbcType="VARCHAR" property="title"/>
    </collection>
</resultMap>


<select id="selectAuthorWithBlog" resultMap="authorWithBlogsResult">
  select a.*,b.* from author2blog ab
  inner join author a
  on a.id = ab.author_id
  inner join blog b
  on b.id = ab.blog_id
  where ab.id = #{id}
</select>
```

## pojo

```java
public class AuthorWithBlogs {
  private int id;
  private String name;
  private List<Blog>;    
  public static class Blog {
   private int id;
   private String title;
  }
}
```

假设现在有一个作者写了两篇文章， 调用 `selectAuthorWithBlog` 这个方法，会发现返回的实际数量是 **1** 条，但是如果使用 `pagehelper` 去获取 `total` ，返回的 `total` 是 **2** 条，这个不难理解，上面的 sql 实际执行结果也是 **2** 条，`pagehelper` 的获取 `total` 也是根据对原 sql 的结果进行 `count` 得出的。

# 解决方法

嵌套 select 查询（[Nested Select for Collection](https://mybatis.org/mybatis-3/sqlmap-xml.html#nested-select-for-collection)）

将原本的关联查询，从 mapper 中抽离，使用 **嵌套 select 查询**

```xml
<resultMap id="authorWithBlogsResult" type="AuthorWithBlogs">
    <id column="id" jdbcType="BIGINT" property="id"/>  
    <result column="name" jdbcType="VARCHAR" property="name"/>

    <collection property="blogs" ofType="Blog" select="selectBlogByAuthor" column="id">  
        <result column="blog_id" jdbcType="BIGINT" property="id"/>  
        <result column="title" jdbcType="VARCHAR" property="title"/>
    </collection>
</resultMap>

<select id="selectBlogByAuthor" resultType="Blog">
    select b.id as 'blog_id',b.title from author2blog ab
    inner join blog b
    on ab.blog_id = b.id
    where ab.author_id = #{id}
</select>

<select id="selectAuthorWithBlog" resultMap="authorWithBlogsResult">
  select * from author
</select>
```

主要修改是在 **select="selectBlogByAuthor" column="id"**

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/paging%20problem%20in%20one%20to%20many%20query%20of%20mybatis-01.png)

这个时候 pagehelper 的查询结果就返回正常了。

# 参考链接

[Mybatis中如何实现一对一，一对多的关联查询](https://blog.csdn.net/m0_67942533/article/details/125382052)

[关于mybatis映射文件中表一对多时分页异常解决方法](https://blog.csdn.net/SkyCloud_/article/details/117339388?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-117339388-blog-105325244.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-117339388-blog-105325244.pc_relevant_aa&utm_relevant_index=2)
