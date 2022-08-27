---
title: Flask-SQLAlchemy 多表对单模型
tags: [Flask,Flask Web 开发]

date: 2020-05-23
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
在 Flask 中 Flask-SQLAlchemy 应该是最常用的 ORM 了，通过它来构建 Model  来映射数据库中的表。通常情况下都是一个表对应一个模型，这种方案很简单。那么如果是多个表对应一个模型的情况下，该如何处理呢？接下来结合一个具体的案例来讲解这一内容。

# 案例
现在我有一个数据库 **test.db**,它的表如下：

![1590195419952-4200c916-62c2-4f6e-9c1b-d79859502926.png](http://ww1.sinaimg.cn/large/006wYWbGly1gf74p3v35jj30cc0eg0uy.jpg)

有很多表并且表名类似，而且表名类似的表的结构相同，以 **UPS1_20190716** 为例，看一下它的表结构：
```shell
Id
workTime
device
status
temp
```

那么如何定义Model呢？像下面这样？

```python
class UPS120190719:
	__tablename__ = 'UPS1_20190716'
	Id = db.Column(db.Integer,primary_key=True)
	workTime = db.Column(db.Time)
	...
    
class UPS220190716:
	__tablename__ = 'UPS2_20190716'
	...
```

显然这样子代码不够优雅，有重复的代码，需要优化。那么该怎么解决呢？

# 解决方案

有俩种方案都可以解决上述问题，不过俩种方案应用场景略有不同，可以根据实际情况来决定。

## 类的继承
第一种方法就是利用类可以继承的特性去实现了，我们定义一个表的基类,然后其它 UPS 表继承 UpsBase即可：

```python
class UpsBase:
	Id = db.Column(db.Integer,primary_key=True)
   	workTime = db.Column(db.Time)
   	...

class UPS120190716(UpsBase):
	__tablename__ = 'UPS1_20190716'

class UPS220190716(UpsBase):
	__tablename__ = 'UPS2_20190716'
```



当表的数量固定这是一个很好的解决方案，但是一旦当数据库的表会发生变动，比如隔天增长几张表，显然我们不太可能天天定义新的`Model`，然后重新部署服务的。这个情况就需要用到第二种解决方案了。

## 动态定义表类
同样的这种方法也需要首先定义表的基类：
```python
class UpsBase:
   Id = db.Column(db.Integer,primary_key=True)
   workTime = db.Column(db.Time)
   ...
```

但是这里就不需要定义其它 UPS 表的类了，这里是动态定义，那就意味着，我们只在需要的时候创建，那么如何创建呢？在 Python 中可以使用 type 函数动态创建类，需要注意的时，在创建时需要设置 **__tablename__ **的值。

```python
Ups1_20190716 = type('UPS120190716', (UpsBase, db.Model), {'__tablename__':'UPS1_20190716'})
```

之后就可以创建实例了：

```python
Ups = Ups1_20190716()
```

## 例子

下面补充一个例子：**如果根据表名动态创建表，如果表不存在则创建**

```python3
from flask import Flask,jsonify
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy import inspect


app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///test.sqlite3"
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False

db = SQLAlchemy(app=app)

class UpsBase:
    Id = db.Column(db.Integer,primary_key=True)
    workTime = db.Column(db.Time)

def table_exists(name):
    engine = db.get_engine()
    ret = inspect(engine).has_table(name)
    print('Table "{}" exists: {}'.format(name, ret))
    return ret


@app.route("/<table_name>")
def index(table_name):
    if not table_exists(table_name):
        up_test = dynamic_table(UpsBase, table_name)
        up_test.__table__.create(db.engine)
    return jsonify({"code":200})
    



def dynamic_table(table_base, table_name: str, bind_key='db') -> db.Model:
    """[summary]
    Arguments:
        table_base {Object} -- [表的属性类，包含表的所有列]
        table_name {str} -- [表名]
        bind_key {str} -- [绑定的数据库]
    Returns:
        [Model] -- [对应的 db Model]
    """
    return type(table_name.capitalize(), (table_base, db.Model),
                {'__tablename__': table_name, '__bind_key__': bind_key, '__table_args__': {'extend_existing': True}})()

```

