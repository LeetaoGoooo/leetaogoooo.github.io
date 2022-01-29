---
title: Visio的UML符号使用说明
tags: [工具]

date: 2016-10-14
published: true
hideInList: false
feature: 
isTop: false
---






>[原文链接](http://blog.csdn.net/comb_august/article/details/8839190)

# 关于Visio的UML的符号使用说明
1. 继承（extends）：用空心三角形+实线来表示。
2. 实现接口（implements）：用空心三角形+虚线来表示。（Visio中小圆形加实线） 
3. 关联（Association）：用实线箭头来表示（Visio中实线，二元关联和关联类） 
4. 聚合（Aggregation）：用空心的菱形+实线箭头来表示
5. 组合（Composition）：用实心的菱形+实线箭头来表示（Visio中没有）
6. 依赖（Dependency）：用虚线箭头来表示。（Visio中有调用、导入、实例化等7种依赖关系）。 
7. 基数：连线两端的数字表明这一端的类可以有几个实例，比如：一个鸟应该有两只翅膀。如果一个类可能有无数个实例，则就用'n'来表示。关联、聚合、组合是有基数的。
此外Visio中还包括链接、约束、跟踪、精化、用法关系，包括信号、异常等符号。

# 各种类间的关系
种类间的关系：

1. 关联：类A与类B的实例之间存在特定的对应关系
2. 依赖：类A访问类B提供的服务
3. 聚集：类A为整体类，类B为局部类，类A的对象由类B的对象组合而成
4. 泛化：类A继承类B
5. 实现：类A实现了B接口 

关联可以分为三种：一对一关联、一对多关联、多对多关联，注意：关联还要以分为单向关联和双向关联。

聚合包括普通聚合和强聚合，强聚合不允许拆缷。

# 各种类关系示意图
![](http://ww4.sinaimg.cn/large/d9e82fa4gw1f8cshl7hhqj20ld0p5jxx.jpg)