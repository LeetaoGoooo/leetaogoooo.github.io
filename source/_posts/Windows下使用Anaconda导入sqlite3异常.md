---
title: Windows下使用Anaconda导入sqlite3异常
tags: [Python]

date: 2020-05-20
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
最近在使用 Anaconda 自带的 Python 环境，在交互模式下 `import sqlite3` 出现异常，异常信息如下:


```shell
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "c:\programdata\anaconda3\lib\sqlite3\__init__.py", line 23, in <module>
    from sqlite3.dbapi2 import *
  File "c:\programdata\anaconda3\lib\sqlite3\dbapi2.py", line 27, in <module>
    from _sqlite3 import *
ImportError: DLL load failed: The specified module could not be found.
```
# 错误原因
从报错信息来看，错误原因很明显找不到对应的 `dll`，我也查阅了相关资料，得出了下面的俩种解决方案
## 解决方案一


下载对应的 `dll`，其实只需要下载一个 `sqlite3.dll`,下载地址 [sqlite3.dll](https://sqlite.org/download.html),根据系统位数下载对应的`dll`，然后将下载好的文件放在 Anaconda 默认的 Python.exe 所在的目录。
## 解决方案二


后来我检索了一下 Anaconda 的安装目录发现所需要的 `dll` 已经存在了，但是因为没有添加到 `PATH` 中所以没有检索出来，因此第二个解决办法就是将 `path\to\Anaconda3\Library\bin` 添加到 `PATH` 中，然后重新打开终端就可以解决问题了。


# 参考链接


[Unable to import sqlite3 using Anaconda Python](https://stackoverflow.com/questions/54876404/unable-to-import-sqlite3-using-anaconda-python)
