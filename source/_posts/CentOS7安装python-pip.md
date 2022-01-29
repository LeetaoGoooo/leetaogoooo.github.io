---
title: CentOS7安装python-pip
tags: [Linux,Python]

date: 2017-03-20
published: true
hideInList: false
feature: 
isTop: false
---





> Package epel-release.noarch 0:7-9 will be installed
--> Finished Dependency Resolution
```

### 安装 python-pip

```shell
[root@localhost Desktop]# yum install python-pip
```

安装完成之后,测试是否安装成功

### 测试是否安装成功

```shell
[root@localhost Desktop]# pip --version
pip 8.1.2 from /usr/lib/python2.7/site-packages (python 2.7)
```

### 安装完成后,清理 cache

```shell
[root@localhost Desktop]# yum clean all
Loaded plugins: fastestmirror, langpacks
Cleaning repos: base epel extras updates
Cleaning up everything
Cleaning up list of fastest mirrors
```