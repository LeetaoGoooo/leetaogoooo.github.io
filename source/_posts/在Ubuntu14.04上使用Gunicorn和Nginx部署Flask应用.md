---
title: 在Ubuntu14.04上使用Gunicorn和Nginx部署Flask应用
tags: [Python,Flask]

date: 2017-11-13
published: true
hideInList: false
feature: 
isTop: false
---







最近闲来无事折腾了一下自己的个人公共号,通过 Flask 构建了几个简单的小功能然后部署到服务器上.网上关于部署的教程有很多,这里我就简单介绍一下我的部署步骤.

# 部署的准备工作

更新并安装下述的包

```shell
apt-get update
apt-get install python-pip python-dev nginx
```

# 创建虚拟环境

切到项目所以目录下,由于我使用的是 python 版本是 3.5.2 的,所以通过下述命令去创建虚拟环境

```shell
python -m venv Env # Env 为创建的虚拟环境的名称
```

然后激活环境安装相应的库

```shell
source Env/bin/activate
pip install gunicorn # 这里省略了其他需要安装的包 
```

# 创建 WSGI 文件

通过命令行在项目目录下创建 <code>wsgi.py</code> 文件

```shell
nano wsgi.py # 原来用 vim,后来发现用 nano 也很爽
```

这个文件内容其实很简单就是让程序运行起来:

```python
# wsgi.py
from httpbin import create_app # httpbin 项目目录

application = create_app('default')

if __name__ == '__main__':
    application.run()
```
然后保存

# 使用 Gunicorn 运行应用 

由于服务默认开发端口仅有80和443端口,对此我也懒得修改,直接将服务挂载到80端口进行测试

```shell
gunicorn --bind 0.0.0.0:80 wsgi
```
通过访问我应用的指定的api然后成功获得了数据,然后开始进行下一步

# 创建一个启动脚本

这个脚本顾名思义允许 Ubuntu 自动启动 Gunicorn 从而挂载我的 Flask 应用.在 <code>/etc/init</code> 目录下创建以 <code>.conf</code> 结尾的文件:

```shell
nano /etc/init/wechat.conf
```
文件内容如下:

```shell
# wechat.conf

description "Gunicorn application server running wechat"

start on runlevel [2345]
stop on runlevel [!2345]

respawn
setuid root # 我的是root用户,如果你是在其他用户下运行则将其他改为其他用户名称
setgid www-data

env PATH=/home/py/wechat/Env/bin # python 环境目录
chdir /home/py/wechat
exec gunicorn --workers 3 --bind unix:wechat.sock -m 007 wsgi
```

保存退出,然后尝试运行你的脚本文件

```shell
start wechat
```

想要查看是否运行成功的话，可以使用下述命令:

```shell
ps -aux | grep wechat
```
如果你在输出的结果中看到相应的进程了就说明你的脚本运行成功了.然后就可以进行下一步

# 配置 Nginx 

在这里介绍了如何简单配置 Nginx,但是在实际应用中需要思考一下是否真的需要用到 Nginx,Nginx有很多功能,但是对于像这样简单的小应用,直接 Gunicorn 裸跑也是没毛病的,但是你说你一台服务器挂多个域名,避免 Gunicorn 独占 80 端口，用 Nginx 这个更没毛病.

在 <code>/etc/nginx/sites-available</code> 目录下创建项目配置文件 <code>wechat</code>,没必要将缺省文件删除

```shell
nano /etc/nginx/sites-available/wechat
```

配置文件内容如下:

```shell
server {
    listen 80;
    server_name server_domain_or_IP;

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/py/wechat/wechat.sock;
    }
}
```

保存退出,然后为了确保配置文件生效,将其链接到 <code>sites-enabled</code> 目录下:

```shell
ln -s /etc/nginx/sites-available/wechat /etc/nginx/sites-enabled
```

测试一下配置文件是否有语法错误:

```shell
nginx -t
```
如果看见 succssful 的成功单词就说明配置文件没有任何问题,然后重启 nginx

```shell
service nginx restart 
```
大功告成~
