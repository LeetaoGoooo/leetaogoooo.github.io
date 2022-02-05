---
title: django bulk_create support post_save signal
date: 2022-02-05
tags: [django, orm]
categories:
comments: true
---

django 默认 `bulk_create` 不支持 `signal`，可以通过自定义 `models.Manager` 支持这个特性

```python
class CustomManager(models.Manager):  
	 def bulk_create(self, items, **kwargs):  
		 for i in items:  
		 post_save.send(i.__class__, instance=i, created=True)  
		        return super().bulk_create(items, **kwargs)  
		  
		    def bulk_update(self, items, fields, **kwargs):  
		 super().bulk_update(items, fields, **kwargs)  
		        for i in items:  
		 post_save.send(i.__class__, instance=i, created=False)
```

使用实例:

```python
class Test(db.models):
	objects = CustomManager()
	...
```
