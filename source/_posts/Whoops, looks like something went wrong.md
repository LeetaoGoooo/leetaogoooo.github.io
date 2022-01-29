---
title: Whoops, looks like something went wrong
tags: [Laravel,PHP]

date: 2017-06-07
published: true
hideInList: false
feature: 
isTop: false
---







搭建 Laravel 完成之后,尝试访问默认页面，然后出现了
**Whoops, looks like something went wrong.**的错误提示信息。
![](http://ww1.sinaimg.cn/large/d9e82fa4ly1fgcuuws5uxj211p064mxj.jpg)

# 问题原因

由于 Laravel 对异常进行了封装,所以只显示文字，并没有将详细的异常信息打印出来。

# 解决办法

修改你项目中的配置文件,位置 *app/config/app.php* 在 *line 41*，将

```php
 'debug' => env('APP_DEBUG', False)
 //=> 修改为
 'debug' => env('APP_DEBUG', True)
```

然后就可以根据错误信息调试自己的代码了。

