---
title: any()和all()对比
tags: [Python]

date: 2017-07-18
published: true
hideInList: false
feature: 
isTop: false
---







今天在Leetcode做题的时候,发现讨论区有人的代码出现使用了*any()*函数,查资料过程中,发现了还有一个*all()*,对这两个函数简单的对比总结一下.

## **any(..)**
从python doc中得到如下图所示解释：
![](http://ww1.sinaimg.cn/large/006wYWbGly1fho2144raoj30ft0323yb.jpg)

其意思是对于可迭代对象中有任意一个不为False的时候,返回True,如果可迭代对象为空的话,返回False

### 示例
![](http://ww1.sinaimg.cn/large/006wYWbGly1fho25rwjqwj30fu03ga9u.jpg)

## **all(..)**
从python doc中得到下图所示解释:
![](http://ww1.sinaimg.cn/large/006wYWbGly1fho26v2fy8j30ft0390sj.jpg)

意思是对于可迭代对象中所有值都为True的情况下返回True,如果当可迭代对象为空的时候,也返回True

### 示例

![](http://ww1.sinaimg.cn/large/006wYWbGly1fho2ablm65j30fv03c3ya.jpg)
