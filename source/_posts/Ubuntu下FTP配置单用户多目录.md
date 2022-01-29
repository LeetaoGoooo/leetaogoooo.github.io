---
title: Ubuntu下FTP配置单用户多目录
tags: [Linux]

date: 2018-08-11
published: true
hideInList: false
feature: 
isTop: false
---







关于 Ubuntu 下 FTP 的常用配置这里就直接跳过，不了解的可以点击 [Ubuntu 下安装和配置 FTP 详解](https://www.linuxidc.com/Linux/2017-04/142493.htm) 链接了解一下。这里重点说一下单用户多目录。

正常情况下一个 FTP 用户只能访问一个目录以及该目录下的所有文件，也就是单用户单目录。如何做到单用户多目录呢，先说一下解决办法：

## 解决办法 -- *mount*

使用 **mount** 命令,具体使用方法，让我们通过一个例子说明。

我有一个项目目录，

```
	|———api
	├── assets
	├── components
	├── router
	├── store
	├── styles
	├── utils
	└── views
```

这是项目的目录结构，由于某种需求需要，我希望创建一个 FTP 用户只访问 <code>api</code> 和 <code> views</code>目录。

如何实现这个效果呢？

1.	在其他目录下创建另外的文件夹,这里我们创建一个 <code>example</code> 文件夹
2.	在该文件夹下创建 <code>api</code> 和 <code>views</code> 文件夹
3. 	将目标文件夹的挂载到对应的文件夹下

整个操作下涉及到的所有命令行:

```bash
mkdir example
cd example
mkdir api
mkdir views
mount --bind /pathto/source/api/ /pathto/example/api/
mount --bind /pathto/source/views/ /pathto/example/views/
```

然后指定相应的 FTP 用户的根目录为 <code>example</code>就大功告成了。
