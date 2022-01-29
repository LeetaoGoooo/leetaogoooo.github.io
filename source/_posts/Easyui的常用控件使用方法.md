---
title: Easyui的常用控件使用方法
tags: [Javascript,前端插件]

date: 2016-10-09
published: true
hideInList: false
feature: 
isTop: false
---






### 获取combobox选中的值,文本
```javascript
$('#com').combobox('getValue') //获取当前选中的值
$('#com').combobox('getText') //获取当前选中的文字
```
### combotree设置默认值
```javascript
$('#combotree').combotree('setValue',value);
```

### 禁用与启用combobox
```javascript
$('#combobox').combobox('disable',true)
$('#combobox').combobox('enable')
```

### combobox的隐藏与显示
```javascript
$('#combobox + .combo').hide()
$('#combobox + .combo').show()
```

### 获取textarea的值
```javascript
$('#textarea').val()
```

### 禁用与启用textarea
```javascript
document.getElementById('textarea').disabled=true;
document.getElementById('textarea').disabled=false
```

### 获取textbox的值
```javascript
$('#textbox').val()
```

### 禁用与启用textbox
```javascript
$('#textbox').textbox('disable',true)
$('#textbox').textbox('enable')
```

### linkbutton的禁用与启用
```javascript
$('#linkbutton').linkbutton('disable');
$('#linkbutton').linkbutton('enable');
```

### form的清空
```javascript
$('#form').form('clear');
```

### 禁用整个form里面的输入框
```javascript
$("#Form :input").attr("readonly", "readonly");
$("input").attr("readonly", "readonly"); 
```

### validatebox的禁用与启用
前两种适用于单个的validatebox;
```javascript
$("#id").attr("readonly", true); 
$("#id").removeAttr("readonly");
$("#id").attr("readonly", "readonly"); 
$("#id").removeAttr("readonly");
```

### 清空validatebox的值
```javascript
$('#id').val("")
```

### datebox禁用启用方法 
```javascript
$("#datebox").datebox('disable'); 
$("#datebox").datebox('enable');
```

### datetimebox禁用启用方法
```javascript
$("#datetimebox").datetimebox('disable');
$('#datetimebox').datatimebox('enable');
```

### TextArea禁止拉动拖动改变大小
1.  彻底禁用拖动:在sytle中添加resize:none
2.  固定大小,右下角拖动图标依然存在
```css
width: x
maxwidth: y
height: mx
maxheight: my
```