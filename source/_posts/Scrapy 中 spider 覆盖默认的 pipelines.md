---
title: Scrapy 中 spider 覆盖默认的 pipelines
tags: [scrapy]

date: 2021-04-22
published: true
hideInList: false
feature: 
isTop: false
---

# 前言
scrapy 是 python 中一个优秀的爬虫框架，基于这个框架，用户可以快速构建自己的爬虫程序。框架涉及很多模块，其中有两个核心概念 items 和 item pipelines。


> **Items**: 爬取的主要目标就是从非结构性的数据源提取结构性数据，例如网页。 Scrapy提供 `Item` 类来满足这样的需求。
> `Item` 对象是种简单的容器，保存了爬取到得数据。 其提供了 类似于词典(dictionary-like) 的API以及用于声明可用字段的简单语法。

> **Item Pipeline**:当Item在Spider中被收集之后，它将会被传递到Item Pipeline，一些组件会按照一定的顺序执行对Item的处理。
> 每个item pipeline组件(有时称之为“Item Pipeline”)是实现了简单方法的Python类。他们接收到Item并通过它执行一些行为，同时也决定此Item是否继续通过pipeline，或是被丢弃而不再进行处理。

``
# Pipeline
通常情况下 scrapy 会有一个默认的 Pipeline：
```python
class KnowledgeGraphPipeline:
    def process_item(self, item, spider):
        pass
```
正常对于传过来的数据，我们通过 item 对应的类型，可以进行判断然后执行逻辑
```python
if isinstance(item, XXItem):
    # 代码逻辑
```
一旦当我们的 spider 多了，将所有的处理逻辑放到一个 pipeline 显然是不合理的，所以会希望每个 spider 都有一个专门的 pipeline，这个时候我们可以通过**复写 spider 的默认配置**就可以实现这样的目的
## 解决办法
```python
class XXSpider(scrapy.Spider):
    name = 'xx'
    custom_settings = {
        'ITEM_PIPELINES': {
            'knowledge_graph.pipelines.xxxx': 200 # 这个值需要大于默认的 pipeline
        }
    }
```
