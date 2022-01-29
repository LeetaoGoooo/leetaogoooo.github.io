---
title: Easyui的datebox始终显示当天时间问题
tags: [Javascript,Easyui]

date: 2017-10-12
published: true
hideInList: false
feature: 
isTop: false
---







之前项目中用上了easyui中的datebox,但是由于datebox 默认的时间格式 <code>m/d/y</code>，并不符合我的要求,于是参考手册将其时间格式修改为<code>y-m-d</code>.格式化的代码如下:

```javascript
$.fn.datebox.defaults.formatter = function(date){
    var y = date.getFullYear();
    var m = date.getMonth()+1;
    var d = date.getDate();
    return y+'-'+m+'-'+d;
}
```
格式是对了,但是出现了下面的问题:
![](http://ww1.sinaimg.cn/large/006wYWbGly1fkf8jl8ahrg30bp06igq4.gif)
没错就是出现了,怎么选都显示当前时间的问题.
最后终于找到的解决方案,在重写格式的同时,还需要将<code>parser</code>重写一遍
```javascript
$.fn.datebox.defaults.parser = function(s){
	var t = Date.parse(s);
	if (!isNaN(t)){
		return new Date(t);
	} else {
		return new Date();
	}
}
```
问题解决~~O(∩_∩)O