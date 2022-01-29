---
title: ModuleNotFoundError No module named PyQt5.sip
tags: [Python,PyQt5]

date: 2018-10-08
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

最近用 pyqt5 开发了一款图形化界面工具,完成之后用 pyinstaller 将其打包成 exe 后出现了问题:

>ModuleNotFoundError: No module named 'PyQt5.sip'


# 解决方法

通过在网上查找一些资料,找到三种办法,网上都有人说成功了.

## 方法一

安装旧版本 pyqt5,原因是: pyinstaller 内置的 sip 版本与最新的 pyqt5 不兼容

```
pip install pyqt5=5.10.1
```

## 方法二

安装新的 sip

```
pip install -U sip 
```

## 方法三

在程序中引入 sip

```
# your code
from PyQt5 import sip
```

第一种方法,成功的解决了我的问题,剩下的两种我尝试了都没有成功

