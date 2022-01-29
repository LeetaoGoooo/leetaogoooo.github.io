---
title: wtforms 使用的一些问题总结
tags: [Python,Flask]

date: 2017-12-04
published: true
hideInList: false
feature: 
isTop: false
---







# Q&A

>Q1.  用IntegerField来限制form只能用数字 但是发现它只能接受1以上 不能接受0 如何设置

A1:添加代码<code>validators=[InputRequired()]</code>  

>Q2. 让 IntegerField 的输入框只允许输入数字,即选渲染后的输入框类型为 number

A2: 添加代码<code>widget=NumberInput()</code>, 引入 <code>from wtforms.widgets.html5 import NumberInput</code>,默认的 widget 为 TextInput(),见链接[[1]](https://github.com/wtforms/wtforms/blob/551bb29ba9ee74e8b4113d08b9b22c5c7df8b9b1/wtforms/fields/core.py#L572)

>Q3. 如何在一个页面中使用两个Form

A3: 确保两个form中的字段不相同,因为渲染之后字段名即为 id,判断语句应如下,其他方法可参考[[2]](https://stackoverflow.com/questions/18290142/multiple-forms-in-a-single-page-using-flask-and-wtforms):
```python
if form1.validate_on_sumite() and form1.submite1.data():
    ... 
elif form2.validate_on_submit() and form2.submit2.date():
    ...
```

>Q4. 添加 placeholder

A4: 添加代码 <code>render_kw={'placeholder': "placeholder"}</code>

# 参考

[[1]wtforms-fields-core.py](https://github.com/wtforms/wtforms/blob/551bb29ba9ee74e8b4113d08b9b22c5c7df8b9b1/wtforms/fields/core.py)
[[2]Multiple forms in a single page using flask and WTForms](https://stackoverflow.com/questions/18290142/multiple-forms-in-a-single-page-using-flask-and-wtforms)
[[3]widgets](http://wtforms.readthedocs.io/en/latest/widgets.html)
[[4]fields](http://wtforms.simplecodes.com/docs/0.6.1/fields.html)

