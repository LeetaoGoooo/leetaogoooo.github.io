---
title: Flask-Restful 中 fileds.DateTime 不支持 strftime 格式
tags: [Flask]

date: 2020-06-06
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
通常情况下，我们会使用Flask来开发网站，但是也有的时候我们只是想用 Flask 来简单的创建 RestFul 的 Api，这个时候可能会使用其他的 Flask 拓展来帮助完成这一切，我前段时间就使用 [Flask-Restful](https://github.com/flask-restful/flask-restful) 这个库去实现简单的接口了。既然是接口必然是提供给别人的使用的，这个时候就涉及到序列化的问题，Flask-Restful 这个库提供了一个 [fields](https://flask-restful.readthedocs.io/en/latest/api.html#module-fields) 模块帮助完成结果的格式化，比如常用的 fields.String,fields.Float,fields.Integer 等等，使用起来也很方便，直到我开始使用 **fileds.DateTime**。


# fields.DateTime
首先看一下 Flask-Restful 中对 fields.DateTime 的说明：
> _class _`fields.``DateTime`(_dt_format='rfc822'_, _**kwargs_)
> Return a formatted datetime string in UTC. Supported formats are RFC 822 and ISO 8601.
> See [`email.utils.formatdate()`](https://docs.python.org/3/library/email.utils.html#email.utils.formatdate) for more info on the RFC 822 format.
> See [`datetime.datetime.isoformat()`](https://docs.python.org/3/library/datetime.html#datetime.datetime.isoformat) for more info on the ISO 8601 format.

通过文档可以得知使用 fields.DateTime 返回的字符串是 UTC 即时间标准时间，很明显和我们不是一个时区的。并且支持的格式只有 [**RFC 822**](https://baike.baidu.com/item/RFC%20822/1468155?fr=aladdin)** **和 **[ISO 8601](https://baike.baidu.com/item/ISO%208601/3910715?fr=aladdin)**，还很贴心的给了说明链接。那么这俩个格式时间是什么样子呢？看一下例子：
```
# RFC 2822,
Fri, 09 Nov 2001 01:08:47 -0000
# ISO 8601
2019-05-18T15:17:08.132263
```
很明显这个 fields.DateTime 不是我们要的时间格式化的 fields，一是时区不对，二是格式也不是我们正常想要的那样。那么怎么解决这个问题？这个时候就需要自定义fileds了。


# 自定义 fields
## fileds.Raw 
官方给的自定义格式是使用 **fields.Raw**，继承 fields.Raw 然后实现 **format** 函数，看下官方的例子：
```python
class UrgentItem(fields.Raw):
    def format(self, value):
        return "Urgent" if value & 0x01 else "Normal"

class UnreadItem(fields.Raw):
    def format(self, value):
        return "Unread" if value & 0x02 else "Read"

fields = {
    'name': fields.String,
    'priority': UrgentItem(attribute='flags'),
    'status': UnreadItem(attribute='flags'),
}
```
显然没有必要，我们已经有了一个现成的 fields.DateTime，那就在它的基础上去完善它。
## CustomDate
代码很简单，新增一个参数 format，当在 dt_format 为 strftime 下使用，默认格式为 **%Y-%m-%d %H:%M:%S**，完整代码如下：
```python
from flask_restful import fields


def _custom_format(dt, fmt):
    if isinstance(dt, str):
        return dt
    return dt.strftime(fmt)


class CustomDate(fields.DateTime):
    '''
    自定义CustomDate,原有的fileds.DateTime序列化后
    只支持 rfc822,ios8601 格式，新增 strftime 格式
    strftime格式下支持 format 参数，默认为 '%Y-%m-%d %H:%M:%S'
    '''

    def __init__(self, dt_format='rfc822', format=None, **kwargs):
        super(fields.DateTime, self).__init__(**kwargs)
        self.dt_format = dt_format
        self.fmt = format if format else '%Y-%m-%d %H:%M:%S'

    def format(self, value):
        if self.dt_format in ('rfc822', 'iso8601'):
            return super(fields.DateTime.format(value))
        elif self.dt_format == 'strftime':
            return _custom_format(value, self.fmt)
        else:
            raise Exception('Unsupported date format %s' % self.dt_format)
```
使用方法很简单和上面使用 fields.Raw 自定义的fields 一样：


```python
fields = {
    'name': CustomDate(dt_format='strftime')
}
```


# 后记
本来准备给 Flask-Restful 提个 PR，把 DateTime 新增 strftime 方法的，后来发现老早之前就有人提了[add a way for Fields.DateTime to custom datetime like strftime](https://github.com/flask-restful/flask-restful/pull/624)，不过一直没有合并，这个项目也不是很活跃了，并且不支持 swagger，小伙伴在想使用 flask 创建 api 的话可以考虑使用[Flask-RESTX](https://github.com/python-restx/flask-restx)支持swagger功能。