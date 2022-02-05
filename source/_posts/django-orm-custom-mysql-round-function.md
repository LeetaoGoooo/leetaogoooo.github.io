---
title: django orm custom mysql round function
date: 2022-01-30
tags: [django, orm]
categories:
comments: true
---

`Django.db.models` 自带的 `Round` 的函数没有不支持小数点位数的保留，默认保留2位，通过自定义的 `Func` 可以实现调用 `MySQL` 的 `ROUND` 函数

<!--more-->

```python
class RoundPlus(Func):  
	 function = 'ROUND'  
	 template = '%(function)s(%(expressions)s, %(precision)s)'  
	  
	 def __init__(self, *expressions, precision=2):  
		 super().__init__(*expressions, precision=precision)
```
