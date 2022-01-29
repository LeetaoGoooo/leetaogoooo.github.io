---
title: Python读写文件之读文件
tags: [Python]

date: 2016-11-11
published: true
hideInList: false
feature: 
isTop: false
---







# 读文件
## 打开文件
读写文件,自然第一步是打开文件,使用**open( )**,其返回一个文件对象,通常有两个参数
```python
open(filena,mode)
```
第一个参数是文件名,第二个参数则是文件对象的访问模式.
访问模式可参照下图:
![](http://ww4.sinaimg.cn/large/d9e82fa4jw1f9ndo67e2sj20fu0dlmzh.jpg)

## 使用实例
测试文件名为 test.txt ,内容如下:
![](http://ww1.sinaimg.cn/large/d9e82fa4jw1f9ndwt2ewdj20ao051jrl.jpg)

```python
>>> f = open('test.txt', 'r')
>>> print f
<open file 'test.txt', mode 'r' at 80a0960>
```
文件打开之后自然就是开始重头戏,读文件了.读文件有很多方式.

## 读取文件方式
接上例,这里 *f* 为上述的文件对象

### read()

```python
>>> f.read()
'1\n2\n3\n4\n5\n6'
>>> f.read()
''
```
这里我们通过 *f.read(size)* 的方式去读取文件,其将读取的内容作为字符串返回.

*size* 是一个可选的参数,当 *size* 缺省或者为负数的时候,返回全部内容.

```python
>>> f = open("test.txt","r")
>>> f.read(-1)
'1\n2\n3\n4\n5\n6'
```

*size* 值合法则返回指定字节的内容,前提是文件大小不超过你的内存,换句话说 *read( )* 这种方式是将全部内容加载到内存中去.

```python
>>> f = open("test.txt","r")
>>> f.read(1)
'1'
>>> f.read(2)
'\n2'
>>> f.read(4)
'\n3\n4'
```

当读到文件末尾的时候,*f.read( )* 将会返回空字符串 ("")

```python
>>> f = open("test.txt","r")
>>> f.read(1)
'1'
>>> f.read(2)
'\n2'
>>> f.read(4)
'\n3\n4'
>>> f.read(4)
'\n5\n6'
>>> f.read(1)
''
```

### readline()
*f.read( )* 返回文件的单独一行.以 "\n" 作为每行的结束,最后一行的使不使用换行符则无所谓.
首先对最初的测试文件 *test.txt* 测试,其最初最后一行没有换行:

```python
>>> f = open("test.txt","r")
>>> f.readline()
'1\n'
>>> f.readline()
'2\n'
>>> f.readline()
'3\n'
>>> f.readline()
'4\n'
>>> f.readline()
'5\n'
>>> f.readline()
'6'
>>> f.readline()
''
>>> 
```

然后这里我们将测试文件 *test.txt* ,在最后一行换行,如下图：
![](http://ww3.sinaimg.cn/large/d9e82fa4jw1f9nv9d7ehnj208t0530sq.jpg)
然后通过 *readline( )* 测试
```python
>>> f = open("test.txt","r")
>>> f.readline()
'1\n'
>>> f.readline()
'2\n'
>>> f.readline()
'3\n'
>>> f.readline()
'4\n'
>>> f.readline()
'5\n'
>>> f.readline()
'6\n'
>>> f.readline()
''
```

通过上述的两个用例,我们可以发现和 *read( )* 一样,如果返回空字符串,则说明到达文件末尾.当空白行被换行符分割,它将作为一个新的一行,这里就不测试了.

# 最后
通过循环去读取一个文件,是最快最有效率的方式,如下:
```python
>>> f = open("test.txt","r")
>>> for line in f:
	print line

	
1

2

3

4

5

6
```
如果你想一次性将所有内容作为 list 返回,可以使用 *list(f)* 或者 *f.readlines()*