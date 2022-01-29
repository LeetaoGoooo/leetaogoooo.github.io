---
title: 国内启动 Docker 自带的 Kubernetes 方法
tags: [Kubernetes]

date: 2019-01-30
published: true
hideInList: false
feature: 
isTop: false
---







# 前言

Docker for Windows/Mac 上的 Kubernetes 启动时需要下载镜像,部分镜像国内无法访问,会出现 Kubernetes 一直 starting 的情况


# 解决办法

Github 上有一个 [k8s-for-docker-desktop](https://github.com/AliyunContainerService/k8s-for-docker-desktop) 的项目,可以解决  18.09/18.06 版本(包含 Kubernetes 1.10.3),18.03 以及  Docker for Mac/Windows 18.09.1 (包含 Kubernetes 1.13.0) 启动问题,不过最新版本的 Docker for Mac/Windows(v2.0.0.2),如下图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fzom77i37lj30oj0k2q5x.jpg)

同样没有办法启动,针对这个我提交了一个 [PR](https://github.com/AliyunContainerService/k8s-for-docker-desktop/pull/17), 也可以参考我 Fork 之后的新建的分支:[v2.0.0.2](https://github.com/lt94/k8s-for-docker-desktop/tree/v2.0.0.2) 按照说明安装启动 Kubernetes