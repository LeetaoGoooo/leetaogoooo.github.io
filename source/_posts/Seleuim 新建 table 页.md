---
title: Seleuim 新建 table 页
tags: [Python,爬虫]

date: 2019-06-19
published: true
hideInList: false
feature: 
isTop: false
---





## 介绍
Seleuim 是一个用于 Web 应用程序测试的工具. Seleuim 可以直接运行在浏览器中,就像真正的用户一样. 因为有这种特性,所有有时候会被用来作为爬虫的一大杀器.


## 使用

通常我们使用 Seleuim 基本上的逻辑都是这样的:

1.  打开一个网页
2.  关闭当前网页,然后打开另外一个网页
3.  然后重复步骤 1,2

从上面我们不难看出,在上述使用情景下,浏览器始终有一个 tab 页存活,当然这种使用方式已经能够满足我们绝大数的需求. 


## 新的需求

那么如果作到打开多个 tab 页呢?

别着急,让我们看代码:

```python
from selenium import webdriver

main_url = 'https://www.leetao94.cn' # URL A
tab_url = 'https://www.baidu.com' # URL B

# 打开 URL A
browser= webdriver.Chrome())
browser.get(main_url)
print("Current Page Title is : %s" %browser.title)

# 打开新的 Tab 页
browser.execute_script("window.open('');")

# 切换到新的 Tab 页,然后打开连接
browser.switch_to.window(browser.window_handles[1])
browser.get(tab_url)

# 新页面的逻辑
print("Current Page Title is : %s" %browser.title)
browser.close()

# 重新返回 URL A
browser.switch_to.window(browser.window_handles[0])
print("Current Page Title is : %s" %browser.title)
```

最后看一下效果:

![](http://ww1.sinaimg.cn/large/006wYWbGly1g46g2l4n38g31l01g8e81.gif)

