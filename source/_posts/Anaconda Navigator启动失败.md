---
title: Anaconda Navigator启动失败
tags: [Anaconda,工具]

date: 2018-05-28
published: true
hideInList: false
feature: 
isTop: false
---







我的最终启动失败的原因是:No Module named 'pywintypes' 缺失引起的 Anaconda Navigator 启动失败,但是解决问题的方法是通用的.

# 定位问题
 
##  找到应用，点击打开文件位置
![](http://ww1.sinaimg.cn/large/006wYWbGly1frr9yiymh6j30r509vwjh.jpg)
 
##  右键快捷方式，将箭头所在栏的内容复制下来
 
![](http://ww1.sinaimg.cn/large/006wYWbGly1frra0iy6sij30lk0tvjsk.jpg)

你会得到类似如下的命令:
>pathto\Anaconda3\pythonw.exe pathoto\Anaconda3\cwp.py pathto\Anaconda3 pathto\Anaconda3\pythonw.exe pathto\Anaconda3\Scripts\anaconda-navigator-script.py

## 打开命令行

打开命令行,将上述命令中的 **pythonw.exe** 换成 **python.exe**, 然后执行,出现 **No Module named 'pywintypes'** 的错误


# 解决问题

在当前命令行使用 conda 激活 base 环境,然后执行下述命令

```
pip install pypiwin32
``` 

安装完成之后,再次打开Anaconda Navigator成功.