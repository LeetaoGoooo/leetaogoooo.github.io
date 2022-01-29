---
title: nginx root 和 alias 配置区别
tags: [Nginx]

date: 2019-01-23
published: true
hideInList: false
feature: 
isTop: false
---






# 前言
前端时间部署自己的博客,使用到了 Nginx ,涉及到一些静态文件, 简单对比一下 nginx 中 alias 和 root 的区别.

# alias

先看一下 alias 的语法:

```
Syntax:	alias path;
Default:	—
Context:	location
```

alias 的功能很简单,替换指定位置,举个简单的例子:

```
location /i/ {
    alias /data/w3/images/;
}
```
当请求 **/i/test.jpg** 的时候,实际请求地址是 **/data/w3/images/test.jpg**, 注意: alias 最后面的 "/" 不能省略.

# root

看一下 root 的语法

```
Syntax:	root path;
Default:	
root html;
Context:	http, server, location, if in location
```

root 则是用来设置根目录的,同样看一个简单的例子:

```
location /i/ {
    root /data/w3;
}
```

当请求 **/i/test.jpg** 的时候,实际请求地址是 **/data/w3/i/test.jpg**,文字可能看起来比较抽象,我们看一张图片,直观的感受一下:

![](http://ww1.sinaimg.cn/large/006wYWbGly1fzga4exaujj30pg0kvjrp.jpg)

# 参考链接

[Nignx-alias](http://nginx.org/en/docs/http/ngx_http_core_module.html#alias)

[Nignx-root](http://nginx.org/en/docs/http/ngx_http_core_module.html#root)

[Nginx虚拟目录alias和root目录](https://www.cnblogs.com/kevingrace/p/6187482.html)