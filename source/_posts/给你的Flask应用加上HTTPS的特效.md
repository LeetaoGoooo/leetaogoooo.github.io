---
title: 给你的Flask应用加上HTTPS的特效
tags: [Python,Flask]

date: 2018-01-21
published: true
hideInList: false
feature: 
isTop: false
---








由于微信小程序请求api接口协议是https的,为了我的小程序,我只好把网站协议由http改成https.在介绍这一过程之前，让我们先看一下什么是https.

# HTTPS 
先看维基百科的定义

>HTTPS (HTTP Secure) is an adaptation of the Hypertext Transfer Protocol (HTTP) for secure communication over a computer network, and is widely used on the Internet.In HTTPS, the communication protocol is encrypted by Transport Layer Security (TLS), or formerly, its predecessor, Secure Sockets Layer (SSL). The protocol is therefore also often referred to as HTTP over TLS,or HTTP over SSL.
The principal motivation for HTTPS is authentication of the accessed website and protection of the privacy and integrity of the exchanged data. It protects against man-in-the-middle attacks. The bidirectional encryption of communications between a client and server protects against eavesdropping and tampering of the communication.In practice, this provides a reasonable assurance that one is communicating without interference by attackers with the website that one intended to communicate with, as opposed to an impostor.
Historically, HTTPS connections were primarily used for payment transactions on the World Wide Web, e-mail and for sensitive transactions in corporate information systems.[citation needed] In the late 2000s and early 2010s, HTTPS was increasingly used for protecting page authenticity on all types of websites, securing accounts and keeping user communications, identity and web browsing private. ————[1]

其翻译过来的大意如下:

Https(HTTPSecure)是超文本传输协议(HTTP)在计算机网络上进行安全通信的一种改编，在互联网上得到了广泛的应用。在HTTPS中，通信协议由传输层安全性(TLS)加密，或者以前的前身安全套接字层(SSL)加密。因此，该协议也经常被称为HTTP over TLS，或HTTP over SSL.

HTTPS的主要动机是对被访问的网站进行身份验证，以及保护所交换数据的隐私和完整性。它能防止中间人的攻击。客户端和服务器之间通信的双向加密保护通信不被窃听和篡改。在实践中，这提供了一个合理的保证，即一个人是在不受攻击者干扰的情况下与他沟通的网站，而不是一个冒名顶替者.

历史上，HTTPS连接主要用于万维网上的支付交易、电子邮件和公司信息系统中的敏感交易.在2000年代末和2010年代初，HTTPS越来越多地被用于保护所有类型网站的页面真实性，保护帐户安全，并保持用户通信、身份和网页浏览的隐私.

# 准备工作 [2]

这里采用的证书是某云的免费的SSL证书

## 申请证书

1. 登录：阿里云控制台，产品与服务，证书服务，购买证书。
2. 购买：证书类型选择 免费型DV SSL，然后完成购买。
3. 补全：在 我的证书 控制台，找到购买的证书，在操作栏里选择 补全。填写证书相关信息。
4. 域名验证：可以选择 DNS，如果域名用了阿里云的 DNS 服务，再勾选一下 证书绑定的域名在 阿里云的云解析。
5. 上传：系统生成 CSR，点一下 创建。
6. 提交审核，审核速度很快

![](http://ww1.sinaimg.cn/large/006wYWbGly1fnob8o5pjij30og0p1wgt.jpg)

申请证书要注意的是验证域名，就是你要验证你想绑定证书的域名是你自己的，如果选择使用 DNS 验证，你需要在域名的管理里，添加一条特定的 DNS 记录，这样就可以证名这个域名是你自己的。使用了阿里云的云解析服务，这个步骤可以自动完成，会自动为你添加一条 DNS 验证的记录。

输入证书要绑定的域名：

![](http://ww1.sinaimg.cn/large/006wYWbGly1fnobcc69juj31h00oyq6o.jpg)

填写个人信息：

![](http://ww1.sinaimg.cn/large/006wYWbGly1fnobeyoiitj31ge10odku.jpg)

## 下载证书

在阿里云的证书管理那里，如果申请的证书审核通过，你就可以下载了，点击 下载，可以选择不同的类型，可以选择 NGINX，或 Apache 之类的服务器。根据自己网站的 Web 服务器类型，下载对应的证书。解压以后，你会得到两个文件一个是 *.key，一个是 *.pem.

![](http://ww1.sinaimg.cn/large/006wYWbGly1fnobgn30rtj31ki0bc404.jpg)


然后通过办法将解压后的文件上传到服务器目录下,我这里就上传到了 <code>/etc/nginx/ssl/wechat</code> 目录下

# 配置 NGINX 的HTTPS

接下来这一段默认你已经完成 [Flask 应用的部署](https://zhuanlan.zhihu.com/p/31005744),如果没有的话,建议先食用后再阅读下面的步骤.

使用命令行打开配置文件 <code>wechat</code>

```shell
nano /etc/nginx/sites-available/wechat
```

原本的内容如下:

```nginx
server {
    listen 80;
    server_name server_domain_or_IP;
    location / {
        include proxy_params;
        proxy_pass http://unix:/home/py/wechat/wechat.sock;
    }
}
```

现修改为如下内容:

```nginx

server {
    listen 443;
    server_name www.shareu.club;
    ssl on;
    root /home/py/wechat;
    ssl_certificate /etc/nginx/ssl/wechat/214452870720595.pem;  # 之前证书解压后的得到的文件
    ssl_certificate_key  /etc/nginx/ssl/wechat/214452870720595.key; # 之前证书解压后的得到的文件
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
    ssl_prefer_server_ciphers on;

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/py/wechat/wechat.sock;
    }
}

server {
    listen 80;
    server_name www.shareu.club;

    return 301 https://$server_name$request_uri;
}

```

然后按照惯例测试一下文件语法有没有错误

```shell
nginx -t 
```

如果看见 succssful 等成功单词就说明配置文件没有任何问题,然后重新加载 nginx 服务

```shell
service nginx reload
```

然后这个时候访问网址就发现协议已经由http变成https了

![](http://ww1.sinaimg.cn/large/006wYWbGly1fnoc7usclhj30a3014a9v.jpg)



# 参考连接

[1][HTTPS](https://en.wikipedia.org/wiki/HTTPS)

[2][用阿里云的免费 SSL 证书让网站从 HTTP 换成 HTTPS](https://ninghao.net/blog/4449)

[记录Amazon EC2环境配置nginx + jekyll + https过程](https://kyle.net.cn/2017/06/08/Amazon-EC2-nginx-jekyll+https/)

[永久免费的SSL证书 - Let's Encrypt 证书部署 HTTPS 并自动续期](https://ubock.com/article/25)