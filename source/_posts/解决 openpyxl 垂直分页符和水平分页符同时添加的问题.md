---
title: 解决 openpyxl 垂直分页符和水平分页符同时添加的问题
tags: [Python]

date: 2018-12-21
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

十天前知乎上有人提问 [python:openpyxl模块怎么给表格添加分页符？实现分页打印功能？](https://www.zhihu.com/question/305289263/answer/549313223),看到问题之后,我很快的给他了一个如何添加垂直分页符或水平分页符的示例,你以为问题就结束了?我是这么以为的,但是事实证明,我太天真了,就在我给出示例的几分钟后,他在我的回答下评论了,说是**同时添加垂直分页符和水平分页符失败了**.
我当时的第一反应:
![](http://ww1.sinaimg.cn/large/006wYWbGly1fyegeyuiomj30j60j63z8.jpg)
心里想着,肯定是他的写法有问题,毫不犹豫的回复到," 没有试过同时添加两种分页符的操作,默认是水平分页符,如果你先添加了垂直分页符的话,应该后面需要重新声明:openpyxl.worksheet.pagebreak.PageBreak.tagname = "rowBreaks",听着自己飞快击打键盘的声音,自己不经有点飘飘然.就在沉浸在自己的YY当中,又过去了几分钟,他用正确的代码错误的结果狠狠的摔在了我的脸上:

```python
col_break = openpyxl.worksheet.pagebreak.Break(5) #创建分页符，参数5：在第5/6中间分页
sheet1.page_breaks.tagname = 'rowBreaks' #分页符属性设置为行分页符
sheet1.page_breaks.append(col_break) #把分页符对象添加到sheet对象里

row_break = openpyxl.worksheet.pagebreak.Break(3) #创建分页符
sheet1.page_breaks.tagname = 'colBreaks' #分页符属性设置为列分页符
sheet1.page_breaks.append(row_break) #把分页符对象添加到sheet对象里
```

"结果是在第3和5列添加了两个垂直分页符，是哪里有问题？大师",他问道.  我看了一眼代码好像没有错,我的心有点慌了,双手开始微微颤抖起来,一遍又一遍地仔细地巡视着代码,视图找出错误反驳他,但是并没有,抱着最后的希望,我把他的代码复制到自己的文件中,然后敲下回车符, excel 文件静静的生成在目录下,这可能是我最后的希望了.拿鼠标的手不自觉的颤抖起来,那么小的屏幕,那么大的文件,鼠标怎么半天都没办法移动上去,我深吸一口气,控制住自己手,终于把鼠标一上去了,双击excel,闭上眼睛,再睁开,我知道,我错了.但是就这么放弃了吗?绝不!我要把这个问题打到!

# 亮剑

> "是时候展示真正的技术了"

俗话说"解铃还须系铃人",我们还得自己看一下问题代码:

```python
# example.py

from openpyxl import Workbook
from openpyxl.compat import range
from openpyxl.utils import get_column_letter
from openpyxl.worksheet.pagebreak import Break, PageBreak

wb = Workbook()
ws = wb.active

for row in range(1, 20):
    for col in range(1,30):
        _ = ws.cell(column=col, row=row, value="{0}".format(get_column_letter(col)))

col_break = Break(5) #创建分页符，参数5：在第5/6中间分页
ws.page_breaks.tagname = 'rowBreaks' #分页符属性设置为行分页符
ws.page_breaks.append(col_break) #把分页符对象添加到sheet对象里

row_break = Break(3) #创建分页符
ws.page_breaks.tagname = 'colBreaks' #分页符属性设置为列分页符
ws.page_breaks.append(row_break) #把分页符对象添加到sheet对象里

wb.save(filename = dest_filename) 
```


从代码上应该是后面的 page_breaks 把前面的覆盖了, 那让我们看看 page_breaks 究竟是什么东西.

```python
class Worksheet(_WorkbookChild):
   # 省略部分代码
      def _setup(self):
        self.page_breaks = PageBreak() # 再看 PageBreak

class PageBreak(Serialisable):
    tagname = "rowBreaks"
    # 省略部分代码
    def append(self, brk=None):
        """
        Add a page break
        """
        vals = list(self.brk)
        if not isinstance(brk, Break):
            brk = Break(id=self.count+1)
        vals.append(brk)
        self.brk = vals
```

从 example 中我们不难发现,我们是通过修改 page_breaks 的 tag_name 去决定插入的分页符是垂直分页符还是水平分页符的.但是 page_breaks 现在只有一个 PageBreak 这就难怪后声明的会把前面的覆盖了,那么如果我们把 page_breaks 变成 PageBreak 的列表呢?

# First Blood -- page_breaks

说改咱就改啊,首先尝试修改 WorkSheet 类

```python
class Worksheet(_WorkbookChild):
   # 省略部分代码
      def _setup(self):
        self.page_breaks = [PageBreak()]
```

然后再修改一下 example.py

```python
from openpyxl import Workbook
from openpyxl.compat import range
from openpyxl.utils import get_column_letter
from openpyxl.worksheet.pagebreak import Break, PageBreak

wb = Workbook()
dest_filename = 'empty_book.xlsx'

ws = wb.active
for row in range(1, 20):
    for col in range(1,30):
        _ = ws.cell(column=col, row=row, value="{0}".format(get_column_letter(col)))

rowPageBreak = PageBreak()
rowPageBreak.tagname = 'rowBreaks'

colPageBreak = PageBreak()
colPageBreak.tagname = 'colBreaks'

ws.page_breaks = [rowPageBreak, colPageBreak] 

ws.page_breaks[0].append(Break(id=5))  
ws.page_breaks[1].append(Break(id=3))
wb.save(filename = dest_filename)
```

敲下回车,心里那个美滋滋,还没高兴几秒钟,就出问题了,果然做人还是得低调一点

```
Traceback (most recent call last):
  File "test.py", line 24, in <module>
    wb.save(filename = dest_filename)
  File "F:\workspace\python\test_openpyxl\test_openpyxl\lib\site-packages\openpyxl\workbook\workbook.py", line 391, in save
    save_workbook(self, filename)
  File "F:\workspace\python\test_openpyxl\test_openpyxl\lib\site-packages\openpyxl\writer\excel.py", line 284, in save_workbook
    writer.save(filename)
  File "F:\workspace\python\test_openpyxl\test_openpyxl\lib\site-packages\openpyxl\writer\excel.py", line 266, in save
    self.write_data()
  File "F:\workspace\python\test_openpyxl\test_openpyxl\lib\site-packages\openpyxl\writer\excel.py", line 83, in write_data
    self._write_worksheets()
  File "F:\workspace\python\test_openpyxl\test_openpyxl\lib\site-packages\openpyxl\writer\excel.py", line 203, in _write_worksheets
    xml = ws._write()
  File "F:\workspace\python\test_openpyxl\test_openpyxl\lib\site-packages\openpyxl\worksheet\worksheet.py", line 893, in _write
    return write_worksheet(self)
  File "F:\workspace\python\test_openpyxl\test_openpyxl\lib\site-packages\openpyxl\writer\worksheet.py", line 151, in write_worksheet
    xf.write(ws.page_breaks.to_tree())
AttributeError: 'list' object has no attribute 'to_tree'
```

看了一眼错误信息,发现了从中作祟的家伙再 worksheet.py 的 151 行, 让我们悄悄地看一眼,打枪的不要.

```python 
# worksheet.py
# 省略部分代码
            if ws.page_breaks:
                    xf.write(ws.page_breaks.to_tree())
```

原来是我们修改了 page_breaks 之后, page_breaks 有时候不再是孤家寡人了,我们需要考虑它有另外的 PageBreak 的情况了.

# Double Kill -- Worksheet

```python
# worksheet.py
# 省略部分代码
            if ws.page_breaks:
                if isinstance(ws.page_breaks,list):
                    for page_break_item in ws.page_breaks:
                        xf.write(page_break_item.to_tree())
                else:
                    xf.write(ws.page_breaks.to_tree())
```
回车,毫无问题,人生啊就是这么寂寞如雪~~~

# 后记

已经在 openpyxl 提了相应的issue,目前再写测试用例,过段时间就提交 PR 了