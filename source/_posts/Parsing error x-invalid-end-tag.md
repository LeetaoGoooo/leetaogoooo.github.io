---
title: Parsing error x-invalid-end-tag
tags: [iview,Vue]

date: 2018-03-26
published: true
hideInList: false
feature: 
isTop: false
---







# 问题

前端时间使用iview进行开发,在运行过程中出现类似下图的错误:
![](http://ww1.sinaimg.cn/large/006wYWbGly1fpq8cymukrj30gi04xmxq.jpg)

# 解决办法

.eslintrc.js 的 rules 加上

```javascript
"vue/no-parsing-error": [2, { "x-invalid-end-tag": false }]
```

# 参考链接

[Parsing error: x-invalid-end-tag](https://github.com/vuejs/vetur/issues/588)

[Col components are wrong in eslint-plugin-vue](https://github.com/iview/iview/issues/2828)

[iview的input标签报错 x-invalid-end-tag](https://segmentfault.com/q/1010000013227727)
