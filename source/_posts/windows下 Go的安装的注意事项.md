---
title: windows下 Go的安装的注意事项
tags: [Go]

date: 2017-12-19
published: true
hideInList: false
feature: 
isTop: false
---







### 安装 **git for windows**

想要使用 <code>go get</code>.需要先安装好 git

### 设置 **GOPATH**

使用<code>go get</code>安装的相关包将会保存在改目录下，为了使得安装的相关包立马生效，可以事先在 **GOPATH** 所在目录新建三个文件，分别为 **bin,pkg,src**

### **GOPATH** 和 **GOROOT** 路径不能相同

两者路径相同的话，使用<code>go get</code> 安装会提示相关错误信息