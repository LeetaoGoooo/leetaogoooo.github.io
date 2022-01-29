---
title: Zabbix Web 界面中文乱码
tags: [Zabbix]

date: 2020-12-30
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
通过 Docker 安装 Zabbix Web 界面默认的语言是英文，当切换成中文的时候，有些图标显示的标签就会乱码，不能正常显示，出现这个问题的原因是在于 zabbix 默认的字体对中文支持有问题，所以解决办法就是替换默认的字体。

# 解决问题
首先找到自己的喜欢的字体，这里我用的是 simkai.ttf。

## 将字体复制到容器

```bash
docker cp simkai.ttf zabbix-web-nginx-mysql:/usr/share/zabbix/assets/fonts/
```

**注意**：复制到 zabbix-web 的容器而不是 zabbix-server 容器

## 替换默认字体文件

进入到容器中然后替换默认的字体文件

```bash
docker exec -it  zabbix-web-nginx-mysql bash
cd /usr/share/zabbix/assets/fonts/
mv DejaVuSans.ttf DejaVuSans.ttf.bak # 不放心的话，可以先备份一下原来的字体文件
mv simkai.ttf DejaVuSans.ttf
```

然后刷新页面，乱码的问题就解决了