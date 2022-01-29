---
title: How To Serve Flask Applications with uWSGI and Nginx on Ubuntu 14.04
tags: [Python]

date: 2017-04-10
published: true
hideInList: false
feature: 
isTop: false
---







# 部署过程中出现错误

>[emerg] 10255#0: bind() to 0.0.0.0:80 failed (98: Address already in use)

解决办法,使用如下命令关闭占用80端口的程序

```shell
sudo fuser -k 80/tcp
```

# 准备工作
## 安装相关的软件

使用如下命令行,完成相应的 *python-pip python-dev* 和 *ngnix* 安装

```shell
sudo apt-get update
sudo apt-get install python-pip python-dev nginx
```

## 创建 Python的虚拟环境

参考我的另一文章[为什么使用virtualenv](http://www.leetao94.cn/2016/10/11/why-I-use-virtualenv/)

安装成功之后,然后创建为我们的flask项目创建一个目录，然后在该目录下创建虚拟环境,如下:

```shell
mkdir ~/myproject
cd ~/myproject
virtualenv myprojectenv
```

关于 *virtuelenv* 相关使用这里不再讲述

## 创建flask项目

### 安装 Flask 和 uWSGI

使用 *pip* 安装 Falsk 和 uWSGI,这里我们不需要激活虚拟环境

```shell
pip install uwsgi flask
```

### 创建应用

这里作为演示,我们将会在一个文件中创建一个简单的应用.使用下面命令:

```shell
vim ~/myproject/myproject.py
```

然后在文件中键入下述代码:

```python
from flask import Flask
application = Flask(__name__)

@application.route("/")
def hello():
    return "<h1 style='color:blue'>Hello There!</h1>"

if __name__ == "__main__":
    application.run(host='0.0.0.0')
```

保存并退出,然后我们通过命令行运行我们的应用了:

```shell
python myproject.py
```

然后通过浏览器访问,终端输出的 IP 地址或者域名(xxxx:5000)，然后你可能会看见下图:

![](http://ww1.sinaimg.cn/large/d9e82fa4ly1fehvhoila8j205601ea9u.jpg)

### 创建 WSGI 入口

经过刚才验证,我们已经可以确认项目可以运行,接下来我们将创建一个文件，作为我们应用的入口.通过这个文件我们可以很好的与 uWSGI 进行交互.通过命令行创建 *wsgi.py* 文件

```shell
vim ~/myproject/wsgi.py
```

这个文件十分简单,我们可以简单的将我们的 flask 应用实例引入进来，然后运行它

```python
# wsgi.py
from myproject import application

if __name__ == "__main__":
    application.run()
```

## 配置 uWSGI

我们的应用基本完成然后我们应用的入口也成功的创建了,接下来我们就需要开始 uWSGI 的配置了

### 测试 uWSGI 的服务
我们需要做的第一件事就是测试 uWSGI 能不能运行我们的应用.不需要太麻烦，我们只需要通过下述的命令就可以测试:

```shell
uwsgi --socket 0.0.0.0:8000 --protocol=http -w wsgi
```

现在你可以通过浏览器访问你的应用,类似xxxx:8000，如果你看见和刚才一样的页面就说明的测试基本是成功的.如果在此之前你激活了你的虚拟环境,那么接下来记得退出它,接下来的操作都是需要在本地的 Python 环境下.

### 创建 uWSGI 配置文件

同样是在之前的项目文件下创建 *myproject.ini* 文件

```shell
vim ~/myproject/myproject.ini
```

然后在该文件中键入如下内容:

```shell
[uwsgi]
module = wsgi

master = true
processes = 5

socket = myproject.sock
chmod-socket = 660
vacuum = true

die-on-term = true
```
输入完成之后保存并退出.

### 创建启动脚本

创建启动脚本的目的就是为了在服务运行的时候可以运行我们的 Flask 应用.我们需要在*/etc/init*目录下创建一个 *.conf*的文件,这里我们就以 *myproject.conf*为例

```shell
sudo vim /etc/init/myproject.conf
```

然后键入如下内容:

```shell
description "uWSGI server instance configured to serve myproject"

start on runlevel [2345]
stop on runlevel [!2345]

setuid user
setgid www-data

env PATH=/home/user/myproject/myprojectenv/bin
chdir /home/user/myproject
exec uwsgi --ini myproject.ini
```
添加完成后保存并退出,接下来我们就可以通过命令启动该进程了

```shell
sudo start myproject
```

## 配置 Nginx 反向代理
我们的 uWSGI 应用现在已经被启动并运行了,在项目目录下等待着请求.我们还需要通过配置 Nginx 将 web 请求转发到该接口上.

### 创建 Nginx 配置文件

```shell
sudo vim /etc/nginx/sites-available/myproject
```

然后键入如下命令:

```shell
server {
    listen 80;
    server_name server_domain_or_IP;

    location / {
        include uwsgi_params;
        uwsgi_pass unix:/home/user/myproject/myproject.sock;
    }
}
```
添加完成后保存并退出.为了保证我们的配置能够生效,我们需要将文件链接到 *sites-enabled* 目录下:

```shell
sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled
```

我们可以通过下述命令测试我们创建的文件语法是否正确:

```shell
sudo nginx -t
```
如果没有返回任何错误信息,我们可以重启 Nginx 重新加载我们的配置

```shell
sudo service nginix restart
```

现在你可以通过浏览器直接访问你的域名或者IP地址看到我们的应用了.

>参考地址:https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-uwsgi-and-nginx-on-ubuntu-14-04
