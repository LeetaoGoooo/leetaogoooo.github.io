---
title: Python读写文件之写文件
tags: [Python]

date: 2016-11-11
published: true
hideInList: false
feature: 
isTop: false
---







文件读完了,我们就得开始尝试写文件了.关于读文件这里不再重复,可以参考[Python读写文件之读文件](http://www.leetao94.cn/2016/11/11/Python%E8%AF%BB%E5%86%99%E6%96%87%E4%BB%B6%E4%B9%8B%E8%AF%BB%E6%96%87%E4%BB%B6/)

# 写文件
与 *read()* 相对应的就是 **write(string)** 了,将内容写进文件当中,返回值为 *None*.这里我们的测试文件同样是 *test.txt*, 只不过我们将其内容清空了.
![](http://ww3.sinaimg.cn/large/d9e82fa4jw1f9ohez44sbj209e083dg0.jpg)
不管读文件还是写文件,第一步都是需要打开文件的,同样关于访问模式这里也不再多说.

```python
>>> f = open("test.txt","r+")
>>> f.write("1\n")
```
是不是这样子就将内容写入到文件中了呢?不急,让我们打开文件看一看:
![](http://ww2.sinaimg.cn/large/d9e82fa4jw1f9ohoddgkij208n04k3yi.jpg)
为了和最初文件区分,特意将顶部部分截图没有截上.通过文件我们发现,我们似乎并没有将内容写到文本中,这是为什么呢?当我们操作做文件的时候,需要使用 *f.close()* 去关闭文件对象,然后释放由打开文件占用的系统资源,然后在关闭之前会将内容写到文本中去.
![](http://ww1.sinaimg.cn/large/d9e82fa4jw1f9ohvhi90nj20ep02jq36.jpg)
一旦关闭后,在尝试使用文件对象,则会失败.
![](http://ww2.sinaimg.cn/large/d9e82fa4jw1f9ohwmbmnij20a004awf5.jpg)
如果留心看上面的 *write*()* 函数,会发现括号里面的内容是 String,所以如果你要想向文件写入非字符串的内容的话,第一步你需要做的是就是将它转换为 String 类型.

```python
>>> f=open("test.txt","r+")
>>> value = ('the answer',42)
>>> s = str(value)
>>> f.write(s)
>>> f.close()
```
![](http://ww4.sinaimg.cn/large/d9e82fa4jw1f9oi9k40zvj209o04jdg0.jpg)
关于文件写的操作大概就是这样了,最后再介绍函数.

## tell()
返回一个数字,文件指针当前在文件中的位置(当前位置到文件开头位置的字节数)
实例如下(在操作前我们已经清空文本):
```python
>>> f=open("test.txt","r+")
>>> f.write("0123456789abcdef")
>>> f.seek(5)  # 将指针移动到文件中的第6字节处
>>> f.read(1)
'5'
>>> f.tell()
6L
```
上述中我们还是用了一个函数 **seek()**,这是我们要提到的第二个额外的函数

## seek(offset,from_what)
第一个参数 offset,顾名思义是偏移量,第二个参数这是指针从什么位置开始,0代表从头开始，1代表当前位置，2代表文件最末尾位置,默认值为0
实例如下(在操作前我们已经清空文本):
```python
>>> f=open("test.txt","r+")
>>> f.write("0123456789abcdef")
>>> f.seek(5)
>>> f.read(1)
'5'
>>> f.seek(-3,2)
>>> f.read(1)
'd'
```

# 最后
推荐关于文件操作的时候使用 **with** 关键词.使用这个关键词的好处是,哪怕在处理过程中发生异常,文件也可以正常关闭.除此之外,它也比 **try-finally** 写起来要短.