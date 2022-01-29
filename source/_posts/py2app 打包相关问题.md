---
title: py2app 打包相关问题
tags: [PyQt5,Python,py2app]

date: 2019-12-17
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
最近一直在研究用 py2app 给自己写的 PyQt5 应用打包应用，在打包过程中遇到了一次问题，写在这里备忘一下。


# 问题
 
**问题一 AttributeError: module 'string' has no attribute 'maketrans'**

这个问题从网上查找了一下发现好像是 PyQt 的问题，但是到现在也没有被修复。

**解决方法**:修复起来也很简单,由于出问题的地方在 **ascii_upper.py** 文件中，找到这个文件，将 **string** 改成 **str** 然后保存就可以了

**问题二 打包后的应用一直出现  Segmentation fault: 11 错误**

出现这个问题的可能性有很多种，我一开始怀疑是 PyQt5 和 py2app 的问题，后来我尝试写了一个 PyQt5 最小应用，然后成功打包完美运行，自然这个猜测就被排除再外了。最后通过引用的库进行排查，成功的定位到错误了，原因出在我用的 **aiohttp 库**，去github上找了一下发现 [pyinstaller cannot import aiohttp](https://github.com/aio-libs/aiohttp/issues/2301) 这个 issue，最后无奈之下只好把用到 aiohttp 的部分用 requests 重写了，然后打包成功.


# 参考

[py2app : AttributeError: module 'string' has no attribute 'maketrans'](https://stackoverflow.com/questions/58496363/py2app-attributeerror-module-string-has-no-attribute-maketrans)
