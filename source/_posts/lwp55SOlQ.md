---
title: '使用 pre-commit 配合 black、isort 自动格式化 Python 文件'
date: 2021-12-30 08:18:00
tags: []
published: true
hideInList: false
feature: 
isTop: false
---

>为了保证 Python 的代码规范，在使用 `git commit` 提交代码之前，需要使用 blake、isort 工具对提交的文件进行格式化，如果提交的代码符合规法则 commit 成功，否则自动格式化文件，然后重新 commit

整个工作流大概是这样子：

![](https://www.leetao94.cn/post-images/1640823506631.png)

其中 `black` 是代码格式化工具，可以通过 `pip install black` 后直接使用，使用方法如下:

```
black {source_file_or_directory}...
```

同时也支持配置文件自定义规则，详细内容可以参考官方文档 [The uncompromising code formatter — Black](https://black.readthedocs.io/en/stable/)

`isort`  则是用来规范 python 库的引入的，按字母顺序对 packages 进行排序，并自动分为不同的部分和类型，同样可以通过 `pip install isort` 后直接使用，使用方法如下:

```
isort mypythonfile.py mypython file2.py
```

和 `black` 一样也支持配置文件自定义规则，具体内容参考官网 [isort (pycqa.github.io)](https://pycqa.github.io/isort/)

`pre-commit` 是整个工作流最重要的一环，`pre-commit` 是 `git-hooks` 中的一个重要的钩子，它在键入提交信息前运行。**可以用于检查即将提交的快照，例如，检查是否有所遗漏，确保测试运行，以及核查代码。 如果该钩子以非零值退出，Git 将放弃此次提交。** 上面提到的机制是整个工作流可以进行的关键。

完全可以自定义 `pre-commit` 钩子的，但是如果只是为了检验的话，可以使用现成的方案 [pre-commit/pre-commit](https://github.com/pre-commit/pre-commit),用 Python 构建，支持多语言的管理器。通过 `pre-commit` 这个库，简单地几步就可以实现自动化工作流。

1. 安装 pre-commit

```
pip install pre-commit
```

然后通过 `pre-commit --version` 确定是否安装成功

```
$ pre-commit --version
pre-commit 2.16.0
```

2. 添加 `.pre-commit-config.yaml` 的配置文件

可以通过 `pre-commit sample-config` 生成一个默认的配置文件,这里贴一下关于 `black` 和 `isort` 的配置文件

```yaml
repos:  
-   repo: https://github.com/psf/black  
 rev: 21.12b0  
 hooks:  
    - id: black  
  
-   repo: https://github.com/PyCQA/isort  
 rev: 5.10.1  
 hooks:  
    - id: isort
```
支持的配置项很多，具体参考 [plugins](https://pre-commit.com/#plugins)

3.  安装 git hooks 脚本

```
$ pre-commit install
pre-commit installed at .git/hooks/pre-commit
```

然后就就大功告成了

![](https://www.leetao94.cn/post-images/1640823519964.png)