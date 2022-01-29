---
title: 'Django 自定义 SearchFilter兼容空格搜索'
date: 2021-12-09 08:29:49
tags: [python,django]
published: true
hideInList: false
feature: /post-images/GLNcAwN1I.jpeg
isTop: false
---
# 前言

Django 自带的 SearchFilter 是不支持空格搜索的，如果需要支持空格搜索并且保留之前的搜索功能则需要自定义 SearchFilter。

# 自定义 SearchFilter

继承 SeachFilter 然后实现 `get_search_terms` 方法，如果同时支持 space 然后又可以保持之前的搜索特性呢？

简单的做法就是，对输入的参数进行校验，如果只有含有空格就返回空格，否则就执行之前的搜索逻辑

```python
class CanSerachBothCharFilter(SearchFilter):

    def get_search_terms(self, request):
        params = request.query_params.get(self.search_param, '')
        # 判断原来的参数是否有只有空格
        only_space = False
        if len(params) != 0 and len(params.strip()) == 0:
            only_space = True

        params = params.replace('\x00', '')  # strip null characters
        params = params.replace(',', ' ')
        if only_space:
            return [params]
        return params.split()
```