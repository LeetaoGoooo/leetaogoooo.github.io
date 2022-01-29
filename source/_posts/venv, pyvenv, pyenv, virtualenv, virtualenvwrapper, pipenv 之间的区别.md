---
title: venv, pyvenv, pyenv, virtualenv, virtualenvwrapper, pipenv 之间的区别
tags: [Python]

date: 2017-10-11
published: true
hideInList: false
feature: 
isTop: false
---







关于python的版本管理,库管理使用不少工具,现在对这些工具进行一下简单的总结.

# PyPI

## virtualenv
virtualenv 是一个非常实用的三方包,可以为 Python 创建各自独立开的python库.具体用法可以参考我之前的文章[为什么使用virtualenv](http://www.leetao94.cn/2016/10/11/why-I-use-virtualenv/)

## pyenv
pyenv 是用来隔离开各种版本的 Python. 举个例子,比如你想在各个版本的Python中测试你的代码,你就需要一个方法去切换各种版本的Python. 通过使用 pyenv,激活你所需要的版本,它将<code>~/.pyenv/shims</code>作为 Python 环境变量的前缀,这个路径下包含着一些可以 Python 命令文件(<code>python, pip</code>).这些不是Python附带命令的副本;它们是特殊的脚本，可以根据<code>PYENV_VERVE</code>环境变量,<code>.python-version</code>文件或者<code>~/.pyenv/version</code>动态决定运行哪个版本的 Python.具体安装步骤的可以参考[pyenv-installer](https://github.com/pyenv/pyenv-installer)

接下来的三个我都基本没有使用过

## pyenv-virtualenv
pyenv-virtualenv 是由 pyenv 作者编写的一个插件,它可以使方便地同时使用 pyenv 和 virtualenv. 如果您使用Python3.3或更高版本，pyenv-virtualenv 将尝试运行 **python -m venv**(如果可用)，而不是运行**virtualenv**. 当然如果你不想使用这个比较方便的特性的话,你也可以同时使用 virtualenv 和 pyenv 而不用 pyenv-virtualenv.

## virtualenvwrapper
看它的名称不难猜到它是对 virtualenv 的进一步封装,事实上也是这样它是对 virtualenv 的一组扩展.它提供了 mkviralenv、lssitepackage等命令, 特别是 workon 可以用于在不同的 virtualenv 目录之间切换.

## pyenv-virtualenvwrapper
pyenv-virtualenvwrapper 也是由 pyenv 编写的一个针对 pyenv 的插件,它可以方便地将虚拟包装程序集成到pyenv中.

## pipenv
pipenv 是由 requests 作者编写的,旨在将管道文件、pip和vialenv合并为命令行上的一个命令.具体内容可以去项目主页浏览[pipenv](https://github.com/kennethreitz/pipenv)

# Standard library

## pyenv
pyenv 是 Python3中的附带脚本,但是在 Python3.6中废弃 了,具体内容见[What’s New In Python 3.6](https://docs.python.org/dev/whatsnew/3.6.html#id8)

## venv
venv 是 Python3 中附带的一个包,你可以通过使用 <code>python -m venv</code>使用它,不过在有的发行版中将它分离成了一个单独的发行版包,如 Ubuntu/Debian 中的 python3-env. 它的用途与 virtualenv 类似，工作方式也非常相似，但它不需要复制Python二进制文件(Windows除外).


# 参考文章
1. https://stackoverflow.com/questions/41573587/what-is-the-difference-between-venv-pyvenv-pyenv-virtualenv-virtualenvwrappe

2. https://stackoverflow.com/questions/29950300/what-is-the-relationship-between-virtualenv-and-pyenv