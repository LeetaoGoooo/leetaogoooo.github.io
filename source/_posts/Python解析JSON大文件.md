---
title: Python解析JSON大文件
tags: [Python,JSON]
date: 2022-03-30
categories: ['优化']
comments: true
---


# 前言

在实际开发中，有时候需要用 Python 去处理一些 JSON 文件，一旦 JSON 文件过大，就有可能出现加载时间过长，内存消耗过大的问题，甚至会导致内存耗尽。所以如果正确地处理大的 JSON 文件呢？
<!--more-->

# 方案

首先构造一个大的 JSON 文件，这里我们使用一个在 github上开源的一个 json 文件 [large-file.json](https://github.com/json-iterator/test-data/blob/master/large-file.json)，文件大小为 24.9 M

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/%E6%88%AA%E5%B1%8F2022-03-30_14.30.06_1648621812730_0.png)

文件内容大致如下：
``` json
	  [
	    {"id":"2489651045","type":"CreateEvent",
	     "actor":{"id":665991,"login":"petroav","gravatar_id":"","url":"https://api.github.com/users/petroav","avatar_url":"https://avatars.githubusercontent.com/u/665991?"},
	     "repo":{"id":28688495,"name":"petroav/6.828","url":"https://api.github.com/repos/petroav/6.828"},
	     "payload":{"ref":"master","ref_type":"branch","master_branch":"master","description":"Solution to homework and assignments from MIT's 6.828 (Operating Systems Engineering). Done in my spare time.","pusher_type":"user"},
	     "public":true,"created_at":"2015-01-01T15:00:00Z"},
	  ...
	  ]
```

接下来我们将会通过不同的方案去解析这个json文件，然后提取对应的 actor.id 和 repo.id 的内容，借助 fileprofiler 去验证不同方案中内存的消耗情况。

## 使用内置标准库-json
``` python
	  import json
	  
	  with open("large-file.json", "r") as f:
	      data = json.load(f)
	  
	  user_to_repos = {}
	  for record in data:
	      user = record["actor"]["login"]
	      repo = record["repo"]["name"]
	      if user not in user_to_repos:
	          user_to_repos[user] = set()
	      user_to_repos[user].add(repo)
```
通过 fileprofiler 看一下运行结果：

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/%E6%88%AA%E5%B1%8F2022-03-30_%E4%B8%8B%E5%8D%885.08.57_1648631340541_0.png)

从上面的图，不难看出，内存重灾区为两处：
	1. 读取文件
	2. 结果进行 decode

看一下 `json` 模块的[实现源码](https://github.com/python/cpython/blob/15da2a2723245710f1bd2c7cbd5b450532ae7728/Lib/json/__init__.py#L293)，不难发现，在解析之前，`json.load()` 将整个文件都加载到内存当中去了
``` python
	  def load(fp, *, cls=None, object_hook=None, parse_float=None,
	          parse_int=None, parse_constant=None, object_pairs_hook=None, **kw):
	      """Deserialize ``fp`` (a ``.read()``-supporting file-like object containing
	      a JSON document) to a Python object.
	      ...
	      """
	      return loads(fp.read(),...)
```
所以显然易见，如果直接加载文件将会占用大量内存。这里还有一个值得思考的问题，为什么文件大小只有 24.9 M，而使用的内存竟然高达 124 M 呢？这个主要是和 Python 字符串的内存表示 有关，简单看一下例子：
``` python
	  >>> import sys
	  >>> s = "a" * 1000
	  >>> len(s)
	  1000
	  >>> sys.getsizeof(s)
	  1049
	  
	  >>> s2 = "❄" + "a" * 999
	  >>> len(s2)
	  1000
	  >>> sys.getsizeof(s2)
	  2074
	  
	  >>> s3 = "🚩" + "a" * 999
	  >>> len(s3)
	  1000
	  >>> sys.getsizeof(s3)
	  4076
```
三个字符串的长度都是 1000，但是它们占用的内存却是各不相同，所以 JSON 文件占用内存的大小并不是取决于它们包含的字符串的长度，而是字符串里包含什么类似的字符。
回归正题，显然使用内置的标准库 `json` 的效果并不是很理想，所以有没有更好的方案呢？对于大的 JSON 文件并不是一次性加载到内存当中去，而是按需加载，这种方式也有一个名称叫做 - 流（stream）
有很多三方库支持这个特性，接下来我们尝试使用其中一个库 [ijson](https://github.com/ICRAR/ijson)

# 三方库 - ijson

``` python
	  import ijson
	  
	  user_to_repos = {}
	  
	  with open("large-file.json", "r") as f:
	      for record in ijson.items(f, "item"):
	          user = record["actor"]["login"]
	          repo = record["repo"]["name"]
	          if user not in user_to_repos:
	              user_to_repos[user] = set()
	          user_to_repos[user].add(repo)
```

同样使用 fileprofiler 看一下运行结果

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/%E6%88%AA%E5%B1%8F2022-03-30_%E4%B8%8B%E5%8D%886.21.59_1648635722250_0.png)

从图片不难看出来，内存消耗一下子就降低了下来。所以下次有大的 JSON 文件，可以尝试用 `ijson` 库

# 参考链接
  
 [ijson](https://github.com/ICRAR/ijson)
