---
title: php artisan migrate 出错
tags: [PHP,Laravel]

date: 2017-06-07
published: true
hideInList: false
feature: 
isTop: false
---







在执行 *php artisan migrate*出现如下错误:
![](http://ww1.sinaimg.cn/large/d9e82fa4ly1fgcuzkgc7zj20og09xwfm.jpg)

从网上找到了如下的原因和解决办法

# 问题原因

MySql支持的utf8编码最大字符长度为3字节，如果遇到4字节的宽字符就会出现插入异常。三个字节UTF-8最大能编码的Unicode字符是0xffff，即Unicode中的基本多文种平面（BMP）。因而包括Emoji表情（Emoji是一种特殊的Unicode编码）在内的非基本多文种平面的Unicode字符都无法使用MySql的utf8字符集存储。

这也应该就是Laravel 5.4改用4字节长度的utf8mb4字符编码的原因之一。不过要注意的是，只有MySql 5.5.3版本以后才开始支持utf8mb4字符编码（查看版本：selection version();）。如果MySql版本过低，需要进行版本更新。

*注：如果是从Laravel 5.3升级到Laravel 5.4，不需要对字符编码做切换。*

# 解决问题

1. 升级MySql版本到5.5.3以上。
2. 手动配置迁移命令migrate生成的默认字符串长度，在AppServiceProvider中调用Schema::defaultStringLength方法来实现配置：

```php
use Illuminate\Support\Facades\Schema;

/**
* Bootstrap any application services.
*
* @return void
*/
public function boot()
{
   Schema::defaultStringLength(191);
}
```

# 补充 

我的 MySQL 版本是 5.5.53,同样产生了如上的错误.不过发现相应的表还是正常生成了。


# 再次出错

之后通过使用 Artisan 工具新建 Model 类及其附属的 Migration 和 Seeder（数据填充）类,在使用 artisan 生成 Migration出现如下的错误:

![](http://ww1.sinaimg.cn/large/d9e82fa4ly1fgcv521vjlj20o80a9q45.jpg)

由手册可知,执行上述命令行其实是在执行 *database/migrations*中的所有文件的 *up* 函数,于是尝试修改所有的 up 函数,增加如下的判断:

```php
 if(!Schema::hasTable('table')) {
    // 创建表
 }
```


参考文章:
[Laravel 5.4 migrate时报错: Specified key was too long error](https://segmentfault.com/a/1190000008416200)
[Laravel 5.4: 特殊字段太长报错](http://www.cnblogs.com/betx/p/6544090.html)