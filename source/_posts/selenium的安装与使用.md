---
title: selenium的安装与使用
tags: [Python]

date: 2017-02-22
published: true
hideInList: false
feature: 
isTop: false
---







本文所有均在 Ubuntu 下完成
# selenium 安装
使用 *pip3* 安装 selenium

```shell
sudo pip3 install selenium
```

关于 *pip3* 的安装,使用下列命令

```shell
sudo apt-get install python3-pip
```

selenuim 安装成功之后就是使用

# selenuim 使用

使用下面的简单例子测试一下

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Firefox()
driver.get("http://www.python.org")
assert "Python" in driver.title
elem = driver.find_element_by_name("q")
elem.clear()
elem.send_keys("python")
elem.send_keys(Keys.RETURN)
assert "No results found" not in driver.page_source
driver.close()
```

## 使用中错误

>selenium.common.exceptions.WebDriverException: Message: 'geckodriver' executable needs to be in PATH. 

**解决方法**:
1. 下载 [geckdriver](https://github.com/mozilla/geckodriver/releases)
2. 将其解压并使用 *chomod +x* 使其可执行,然后拷贝到 /usr/local/bin 目录下

>selenium.common.exceptions.WebDriverException: Message: Unsupported Marionette protocol version 2, required 3

**解决方法**

将你的 Firfox 浏览器更新,确保你的版本要 >= v47,使用下列命令进行更新

```shell
sudo apt-get update
sudo apt-get install firefox
```
然后重启 firefox

## 运行

```shell
python3 xxx.py
```

然后你会发现 firefox 被打开,跳转到 "http://www.python.org",然后搜索框填入 "python",然后跳转到搜索页面,最后的截图如下:

![](http://ww1.sinaimg.cn/large/d9e82fa4ly1fczhmsyn01j20tg0ii757)