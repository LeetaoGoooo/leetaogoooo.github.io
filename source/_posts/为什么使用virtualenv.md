---
title: 为什么使用virtualenv
tags: [Python]

date: 2016-10-11
published: true
hideInList: false
feature: 
isTop: false
---






# virtualenv 的安装
## Ubuntu 下 virtualenv 的安装
Ubuntu 下的安装 python 的第三方依赖库就不得不提 python 的三大神器之 **pip**.默认情况下 Ubuntu 是没有安装的,可以在命令行下使用*pip xxx* 进行简单测试.

### pip 的安装
pip 安装有很多方法,最简单的就是命令行输入下列命令,然后回车
```shell
 sudo apt-get install python-pip
```
当然还有其他的安装方式,可以参考[How to install pip](https://pip.pypa.io/en/stable/installing/)

pip 安装完成之后,接下来就是重头戏了.
### virtualenv 的安装
在命令行下输入如下命令,然后回车
```shell
 sudo pip install virtualenv
```
同样安装方法有很多种,可以参考[How to install virtualenv](https://virtualenv.pypa.io/en/stable/installation/)

### virtualenv 的简单入门

**创建虚拟环境**
```shell
virtualenv ENV
```

创建一个名为 **ENV**的文件夹,在创建环境时会依赖系统环境的site-packages，如果想完全不依赖系统的site-packages，可以加上参数--no-site-packages来建立虚拟环境.
然后进入到**ENV**的目录下,目录下有如下文件夹:
```shell
$:~/ENV$ ls
bin  include  lib  local  pip-selfcheck.json
```
**激活虚拟环境**
```shell
source bin/activate
```
激活成功后,命令行前面会出现**(ENV)**的符号,如下图所示:
![](http://ww4.sinaimg.cn/large/d9e82fa4jw1f8njgfw9bcj20k200yt8w.jpg)
**注意**:
1. *ENV/lib* 和 *ENV/include* 包含了该环境下所支持的库.在当前环境下安装的包将会保存在 *ENV/lib/pythonX.X/site-packages* 下
2. *ENV/bin* 则是 编译器所在之处了,在当前环境下你的 python 脚本文件的头部写法应该是这样的 *#!/path/to/ENV/bin/python*
3. 在 virtualenv 环境下 pip 和 setuptools 是默认安装的,然后你可以很容易的安装其他的三方库

**退出虚拟环境**
```shell
deactivate
```

## Windows 下 virtualenv 的安装
windows 下安装 virtualenv 跟Ubuntu下安装大同小异,实现安装好 pip,然后将其路径添加到系统环境变量,然后紧接着安装 virtualenv ,然后完成之后尝试在任意路径新建新的虚拟环境了.

# 为什么要使用 virtualenv
老实说最初我并不怎么喜欢使用 **virtualenv**,但是随着后来写的代码越来越多,项目所依赖的第三库也各不相同,需要别人去协助的我的时候,由于没有使用**virtualenv**,导致本地的 python 中的三方库十分的多,这样导致还需要人工去判断当前项目使用哪些库何其麻烦.而 virtualenv 的作用就是用来建立一个虚拟的python环境，一个专属于项目的python环境,保持一个相当干净的环境
使用 **virtualenv** 后导出已安装的包,只需要如下命令:
```shell
pip freeze > <目录>/requirements.txt
```
然后别人安装你的依赖,也只需要一个简单的命令:
```shell
pip install -r requirements.txt
```