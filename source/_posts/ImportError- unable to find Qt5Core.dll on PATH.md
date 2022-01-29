---
title: ImportError- unable to find Qt5Core.dll on PATH
tags: [Python,PyQt5]

date: 2019-10-15
published: true
hideInList: false
feature: 
isTop: false
---



# 前言

今天在 windows 下打包 PyQt5 应用出现了 **ImportError: unable to find Qt5Core.dll on PATH** 错误，搜索了一下找到以下解决办法


# 解决办法

## 使用 --path 参数

由于我们打包出错的原因是缺少 **Qt5Core.dll**，所以在打包过程中，指定路径。使用方法如下

```
pyinstaller --path path_to_\Python\Lib\site-packages\PyQt5\Qt\bin demo.py
```

这种方法没有成功，从打包信息来看，pyinstaller 并没有使用我指定的路径。github 也有人提到这个问题

![1571143796623.jpg](http://ww1.sinaimg.cn/large/d9e82fa4ly1g7z6dero3nj20ii01idg1.jpg)

具体信息可以点击 [PyQT 5.7 DLLs are not bundled ](https://github.com/pyinstaller/pyinstaller/issues/2152) 查看

## 升级 pyinstaller 

也有人提到升级 pyinstaller 到最新版本可以解决，同样没有成功。使用方法：

```
pip install --upgrade pyinstaller
```

## 构建导入依赖的脚本

最后在 stackoverflow 中找到了解决办法 [How to fix “ImportError: unable to find Qt5Core.dll on PATH” after pyinstaller bundled the python application](https://stackoverflow.com/questions/56949297/how-to-fix-importerror-unable-to-find-qt5core-dll-on-path-after-pyinstaller-b)，通过构建一个依赖脚本，在引入 PyQt5 相关库之前，指定依赖的路径。

```
import fix_qt_import_error # 依赖脚本

from PyQt5.QtWidgets import *
from PyQt5.QtCore import *
from PyQt5.QtGui import *
```

这个成功解决了我的问题，下面是脚本具体内容 `fix_qt_import_error.py` 的具体内容

```
# Fix qt import error
# Include this file before import PyQt5
import os
import sys
import logging
 
 
def _append_run_path():
    if getattr(sys, 'frozen', False):
        pathlist = []
 
        # If the application is run as a bundle, the pyInstaller bootloader
        # extends the sys module by a flag frozen=True and sets the app
        # path into variable _MEIPASS'.
        pathlist.append(sys._MEIPASS)
 
        # the application exe path
        _main_app_path = os.path.dirname(sys.executable)
        pathlist.append(_main_app_path)
 
        # append to system path enviroment
        os.environ["PATH"] += os.pathsep + os.pathsep.join(pathlist)
 
    logging.error("current PATH: %s", os.environ['PATH'])

 
_append_run_path()
```

# 猜想

之前没有安装 Anaconda，打包一切正常，默认会使用虚拟环境的 python 解释器，现在打包的时候，发现默认使用的解释器是 Anaconda 的 base 环境下的，可能这也是问题的原因之一，有待后续考证。。。