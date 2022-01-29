---
title: Windows下Docker出现net http-TLS handshake timeout
tags: [Docker]

date: 2018-02-08
published: true
hideInList: false
feature: 
isTop: false
---






# 原因

docker默认镜像拉取地址为国外仓库下载速度较慢,则会报错"net/http: TLS handshake timeout"

# 解决办法

将镜像源切换位国内的.右键 Docker 图标 => Settings

![](http://ww1.sinaimg.cn/large/006wYWbGly1fo90g35908j308f08kmy3.jpg)

然后按照下图的步骤

![](http://ww1.sinaimg.cn/large/006wYWbGly1fo90gqqhrcj30yb0mc0vv.jpg)

其中Registry mirrors 填入内容为:
>http://69292621.m.daocloud.io

然后 Apply 之后等待重启,然后大功告成.
