---
title: KindleGen bad CPU type in executable
tags: [工具]

date: 2020-06-26
published: true
hideInList: false
feature: 
isTop: false
---



# 前言

最近准备用 kindleGen 将一些 RSS 生产 mobi 然后推送到 kindle 上去，发现 kindleGen 没有办法在 macOS 下使用了，使用的时候会出现错误：

```bash
bad CPU type in executable
```

# 问题

找了一些资料，最后发现原因是`macOS Catalina 10.15 不再支持 32 位应用，而亚马逊并没有升级 kindlegen`

# 解决办法

亚马逊更新了 kindle-previewer，支持 macOS Catalina 10.15，而 kindle-previewer 的包里带有 kindlegen。

下载安装 kindle-previewer，安装完成之后查看应用程序的包内容，在 `/Contents/MacOS/lib/fc/bin ` 中找到 `kindlegen`