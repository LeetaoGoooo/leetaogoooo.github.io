---
title: Flask Web 开发 -- 开发博客应用 -- 第一部分 安装
tags: [Flask,Flask Web 开发,Python]

date: 2018-12-16
published: true
hideInList: false
feature: 
isTop: false
---







# 写在前面

该项目在 Ubuntu16.04 平台下开发,基于 Flask 版本 1.0.2, 项目基本完成,现已开源,源码地址 : [peach-blog](https://github.com/lt94/peach-blog.git), 网站实际运行效果: [Leetao's Blog](http:www.leetao94.cn)

# 环境的搭建

> 本项目中的项目代码基于 Python 3.6 | 3.5 开发完成,建议使用以上两个版本

## 1.1 使用虚拟环境

关于为什么使用虚拟环境,可以参考很久之前我写的一篇文章 [为什么使用virtualenv](http://www.leetao94.cn/post/16), 尽管我们接下来使用 virtualenv ,但是终究也是虚拟环境,其核心思想还是不变的: 可以避免各个项目环境的包的混乱和版本冲突,保持项目 python 环境干净,便于后期别人维护以及项目迁移.

由于我们使用的是 python3.+,其自带了 venv, 可以创建虚拟环境, 对于 Ubuntu 则需要通过下述命令手动安装

```
sudo apt install python3-venv
```

准备工作做好之后,我们需要创建一个文件夹 peach-blog

```
mkdir peach-blog
```

windows 下的文件夹的创建这里就不多说了. 

下一步是使用 venv 在 peach-blog 文件中创建虚拟环境,这个命令和 virtualenv 类似,同样需要一个必须的参数,就是虚拟环境的名字,创建虚拟环境成功中,当前文件夹会出现一个子文件夹,名字就是上述命令中指定的参数, 与虚拟环境相关的文件都保存在这个文件夹中,个人习惯喜欢把虚拟环境命名为 env

```
python3 -m venv env
```

如果是 windows 下,默认安装的是 python3.5+ 的话,创建命令是:


```
python -m venv env # 确保 python 路径被添加到环境变量中
```

现在在 peach-blog 文件夹下有一个名为 env 的子文件夹, 它里面保存了一个全新的 python 环境 (与 windows 下不同的是, windows 下 env 中还有 Python 的二进制文件), 需要使用这个虚拟环境的话,再次之前我们首先需要激活它,可以通过下述命令激活:

```
source env/bin/activate
```

在 windows 下激活命令如下:

```
env\Script\activate
```

虚拟环境激活后,其命令行会出现虚拟环境的名称,提示你已经激活环境成功

```
(env) $
```

当我们不想使用虚拟环境的时候,我们可以通过 deactivate 这个命令退出当前的虚拟环境.

## 1.2 使用 pip 安装项目开发必须的包

大多数 Python 包都使用 pip 工具安装，在我们创建虚拟环境后, 其环境中已经包含 pip. 激活虚拟环境后，pip 就可以正常的使用了.

由于项目最终使用的依赖包比较多,但是在项目初期,我们并不需将其全部安装,我们可以先安装下述 Python 包:

```
Flask
Flask-sqlalchemy
Flask-migrate
Flask-bootstrap
Flask-wtf
```

执行下述命令在虚拟环境中安装上述包:

```
(env) $ pip install flask flask-sqlalchemy flask-migrate flask-bootstrap flask-wtf
```

如果没有报错的话,基本上说明已经正确的安装这些包了.在下次的教程中就可以尝试搭建其项目的最初的框架了.