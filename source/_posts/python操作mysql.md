---
title: python操作mysql
tags: [Python,MySQL]

date: 2017-10-11
published: true
hideInList: false
feature: 
isTop: false
---







# 环境的安装
## windows 下 mysql-python 的安装
从官网下载对应版本的软件:
64位:[MySQL-python-1.2.3.win-amd64-py2.7.exe](http://pan.baidu.com/s/1c1W9FXY)
32位:[MySQL-python-1.2.5.win32-py2.7.exe](http://pan.baidu.com/s/1jI9DeOQ)
下载完成之后点击安装一路next就完成了,安装完成之后在命令行下进行简单的测试
![](http://ww3.sinaimg.cn/large/d9e82fa4jw1f8okr3dogej20ht03hjro.jpg)
如果没有保存的话就说明 mysql-python 成功安装到本地了

## mysql-python的简单使用
### 创建一张测试表
```sql
CREATE TABLE `t_user` (
	`id` INT(11) NOT NULL AUTO_INCREMENT,
	`name` VARCHAR(255) NULL DEFAULT NULL,
	`age` VARCHAR(255) NULL DEFAULT NULL,
	PRIMARY KEY (`id`)
)
COLLATE='utf8_general_ci'
ENGINE=InnoDB
AUTO_INCREMENT=9;
```
在mysql下执行上述语句创建一张 user 表

### 插入
```python
#-*- coding:utf-8 -*-
"""
author:leetao
"""
import MySQLdb
        
conn=MySQLdb.connect(host="localhost",user="root",passwd="",db="test",charset="utf8")    
cursor = conn.cursor()      
         
sql = "insert into t_user(name,age) values(%s,%s)"     
param = ("leetao",'22')      
cursor.execute(sql,param)

cursor.close()
conn.commit()
conn.close()
```
执行完插入语句之后,让我们检测一下是否将数据插入到数据库中
![](http://ww4.sinaimg.cn/large/d9e82fa4jw1f8olulb8irj20hy0440sy.jpg)
成功插入数据库,great!

### 修改
```python
#-*- coding:utf-8 -*-
"""
author:leetao
"""
import MySQLdb
        
conn=MySQLdb.connect(host="localhost",user="root",passwd="",db="test",charset="utf8")    
cursor = conn.cursor()      
         
sql = "update t_user set name=%s where id = 1"     
param = ("lt")      
cursor.execute(sql,param) 

cursor.close()
conn.commit()
conn.close()
```
同样的执行完脚本，然后查看修改是否发生效果
![](http://ww1.sinaimg.cn/large/d9e82fa4jw1f8olz8fwbuj20hx03iwek.jpg)
修改成功,cool~~

### 查询
```python
#-*- coding:utf-8 -*-
"""
author:leetao
"""
import MySQLdb
        
conn=MySQLdb.connect(host="localhost",user="root",passwd="",db="test",charset="utf8")    
cursor = conn.cursor()      
         
sql = "select * from t_user where id = 1"     
cursor.execute(sql)
for rows in cursor.fetchall():
    for row in rows:
        print row

cursor.close()
conn.commit()
conn.close()
```
执行脚本,控制台成功输出如下结果:
![](http://ww4.sinaimg.cn/large/d9e82fa4jw1f8om1nxqbbj20g101omwz.jpg)

### 删除
```python
#-*- coding:utf-8 -*-
"""
author:leetao
"""
import MySQLdb
        
conn=MySQLdb.connect(host="localhost",user="root",passwd="",db="test",charset="utf8")    
cursor = conn.cursor()   
sql = "delete from t_user where id = 1"
cursor.execute(sql)

cursor.close()
conn.commit()
conn.close()
```
执行完删除后，数据库 t_user 表应该不存在任何数据了，通过 sql 语句验证一下结果
![](http://ww2.sinaimg.cn/large/d9e82fa4jw1f8om5yvdaij20hv01tmx1.jpg)

如上就是关于 python 对mysql的所有操作
