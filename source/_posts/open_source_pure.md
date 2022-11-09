---
title:  Pure 基于 Github Discussion 的博客
tags: [Golang,开源]
date: 2022-10-28
published: true
---

# 前言

在过去一年，我一直使用 github action + hexo 的方式去自动化部署我的博客到 github pages，这种方式的好处就是，你只需要配置好一次将代码仓库，然后你就只需要一个浏览器然后写 hexo 格式的 markdown 文件就够了。

下面是我的构建历史记录

<img width="1299" alt="构建历史记录" src="https://user-images.githubusercontent.com/10162120/198286370-4333398a-c452-4358-b678-679a43b073c8.png">

到目前位置累计构建了 93 次，当然并不是意味着我写了 93 篇，更新也会导致文章重新构建。这一切似乎看着还不错，但是有几点一直让我觉得很痛苦：

1. hexo markdown 文章开头的格式
2. 上传图片
3. 文章的更新

hexo 有很多固定的格式，有时候每次写文章，我都不得不去找之前的文章把格式复制过来，然后在重新修改一下。除此之外，就是上传图片这件事情了，尝试过各种图床的方案，有的图床也很好用，但是基本上都需要打开他们的网站，或者借助三方的软件然后将图片上传，这种中断，让我觉得很难受，所以我想有没有更好的方式去写博客。
<!--more-->

# 调研

作为一个程序员，github 是我最喜欢逛的网站了，所以我在想有没有可以基于 github 的仓库构建的网站，然后 github issue 和 github discussion 就成了我的备选项了，所以我检索了这两个关键词

<img width="400" alt="github issue blog" src="https://user-images.githubusercontent.com/10162120/198289445-ec7717f6-1cab-4ab2-b5c4-cc3f22337a16.png"><img width="400" alt="github discussion blog" src="https://user-images.githubusercontent.com/10162120/198289620-7622338b-896e-45a5-943d-35d2ee4ba96d.png">

最后让我发现了 [浮之静](https://github.com/lencx/z)

<img width="1679" alt="浮之静" src="https://user-images.githubusercontent.com/10162120/198290666-1380f9a5-2919-49af-9854-75b733bac6cd.png">

网站挺好看的，但是但是没有部署文档，遂放弃了。

# 折腾

没办法了，没有枪没有炮，敌人给我们造，没有合适的项目，只好自己动手撸了。牺牲了我两个工作日晚上的休息时间，总算把 [Pure](https://github.com/LeetaoGoooo/pure) 初版构建出来了。

<img width="1207" alt="Pure" src="https://user-images.githubusercontent.com/10162120/198291338-e7c090b1-0aaa-4530-956e-66c916dbc0df.png">

部署也很简单，golang 项目通用部署步骤，只需要部署前配置一下四个环境变量（一个非必须的），然后启动项目，配置一下 nginx 就大功告成了。
