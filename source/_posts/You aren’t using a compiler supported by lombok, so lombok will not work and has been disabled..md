---
title: You aren’t using a compiler supported by lombok, so lombok will not work and has been disabled.
tags: [Java,Spring Boot]

date: 2020-12-09
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
前几天将 IntelliJ IDEA 升级到 2020.3 版本，然后在编译项目的时候发现原本正常运行的项目无法编译成功了，提示下面的错误:


> java: You aren’t using a compiler supported by lombok, so lombok will not work and has been disabled. 


在网上检索寻找问题解决办法，发现在 github 上已经有人反馈了这个问题 [[BUG] Lombok Does not work with IntelliJ EAP 2020.3 Build 203.4203.26](https://github.com/rzwitserloot/lombok/issues/2592#) 并给出了解决办法。



# 解决办法


总共发现两种解决办法，两种办法均有效。
## 方法一
设置编译器（compiler），打开设置（File -> Settings）

![](/posts/1607499429.png)

然后选择编译器（**compiler**），在 **Shared build process VM options** 填入: **-Djps.track.ap.dependencies=false**

![](/posts/1607499460.png)

**点击 apply，这个时候就可以重新编译成功了。

## 方法二
第二种方法就比较简单了，将你的 Lombok 升级就可以解决了，升级到  **1.18.14**

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.14</version>
</dependency>
```


# 结尾
本次出现这个问题，IDEA 方拒绝背锅，说是 Lombok 的问题。完整内容见：[Build project with lombok - IntelliJ Ultimate 2020.3 EAP](https://youtrack.jetbrains.com/issue/IDEA-252069)


