---
title: HP EliteDesk 800 G3  SFF 台式机 使用  BiosConfigUtility 配置 Bios
tags: [Trifles]

date: 2017-11-25
published: true
hideInList: false
feature: 
isTop: false
---








在windows下装个docker真是不容易,到最后一步,没有办法开启VTx. 其实很简单,前提如果是可以进入bios的情况下. 不知道怎么会是我的台式机(HP EliteDesk 800 G3  SFF )始终无法进入 bios,开机按 F10 会有声音但是没有任何画面,显示屏没有任何画面,我以为是连接显示器的接口问题,从集显接口换了蓝色的独显接口,但是还是没有任何作用.至到我找到了 BiosConfigUtility 这个 HP 官网的软件.从官网下载软件并安装.安装完成之后进入安装完成界面,如下图:

![](http://ww1.sinaimg.cn/large/006wYWbGly1flthwedk3uj30od07274o.jpg)

然后打开命令行以管理员的身份运行,切换到该目录下,执行下述命令:

![](http://ww1.sinaimg.cn/large/006wYWbGly1flthyrgh8mj30xi083dge.jpg)

你会发现在当前目录下你多了一个 "config64.xml" 的文件,如果你是 32 位的系统,使用不带有 64 后缀的命令,然后使用编辑器打开该 xml,注意以管理员的身份打开该编辑器,否则待会你无法保存这个文件,当然如果你将这个文件保存到非系统盘路径下则不需要.然后找到有关虚拟支持的设置:

![](http://ww1.sinaimg.cn/large/006wYWbGly1flti2pjaedj30z50ostba.jpg)

编辑完成保存之后,在执行如下命令:

```shell
BiosConfigUtility64 /set:"config64.xml"
```

如果控制台输出内容大意为没有错误发生,则说明你设置成功了！然后重启,docker 终于可以正常运行了！！！