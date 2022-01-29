---
title: 'Elasticsearch 支持 PDF 等文件搜索'
date: 2021-09-08 18:28:54
tags: [Elasticsearch]
published: true
hideInList: false
feature: /post-images/07txYm7YT.jpeg
isTop: false
---
# 前言

默认情况下 es 是不支持 pdf、doc 等文档的搜索的，但是可以通过安装 `Ingest attachment plugin` 插件来使得 es 提取通用格式的文件，从而可以实现搜索的功能。

# 安装与使用

## 安装 Ingest attachment plugin

安装很简单，通过 `elasticsearch-plugin` 可以直接进行安装

```bash
bin/elasticsearch-plugin install ingest-attachment
```

> Ingest attachment plugin 允许 Elasticsearch 通过使用 Apache 文本提取库 Tika 提取通用格式（例如：PPT，XLS 和 PDF）的文件附件。Apache Tika 工具包可从一千多种不同的文件类型中检测并提取元数据和文本。所有这些文件类型都可以通过一个界面进行解析，从而使 Tika 对搜索引擎索引，内容分析，翻译等有用。需要注意的是，源字段必须是 Base64 编码的二进制，如果不想增加在 Base64 之间来回转换的开销，则可以使用 CBOR 格式而不是 JSON，并将字段指定为字节数组而不是字符串表示形式，这样处理器将跳过 Base64 解码。

### 创建 attachment pipeline

通过 kibana 的开发工具进行请求

```bash
PUT _ingest/pipeline/pdfattachment
{
  "description": "Extract attachment information encoded in Base64 with UTF-8 charset",
  "processors": [
    {
      "attachment": {
        "field": "file"
      }
    }
  ]
}
```

返回结果：

```json
{
  "acknowledged" : true
}
```

表示创建成功，接下来就是验证上传 pdf 以及搜索功能了。

### 转换并上传PDF文件的内容到Elasticsearch中

对于 Ingest attachment plugin 来说，它的数据必须是 Base64 的。这里为了快速创建，我们通过一个 bash 脚本去处理用来测试的 pdf

```bash
!/bin/bash

encodedPdf=`cat sample.pdf | base64`

json="{\"file\":\"${encodedPdf}\"}"

echo "$json" > json.file

curl -X POST 'http://localhost:9200/pdf-test1/_doc?pipeline=pdfattachment&pretty' -H 'Content-Type: application/json' -d @json.file
```

上传成功会返回如下的结果:

```bash
{
  "_index" : "pdf-test1",
  "_type" : "_doc",
  "_id" : "1oVSxHsB1ubIHqCXIbPU",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

如果出现提示下述提示

```bash
{
	"statusCode": 400,
	"error": "Bad Request",
	"message": "Payload content length greater than maximum allowed: 1048576"
}
```

说明上传的文件大小超过了 Kibana 默认的上限（默认 1M），修改 kibana 的配置 `kibana.yml`

```yaml
server.maxPayloadBytes: "209715200"
```

# 查看索引并搜索

## 查看索引

通过下面的命令可以查看 pdf-test1 的索引情况:

```bash
GET pdf-test1/_search
```

结果如下图
![](https://www.leetao94.cn/post-images/1631096996349.png)


其中 `_source` 里有个 `content` 字段，就是 pdf 的内容，结果如下:

```json
{
  "_source": {
		 "file":"...",
			"attachment": {
                        "date": "2021-08-18T07:29:34Z",
                        "content_type": "application/pdf",
                        "author": "author",
                        "language": "lt",
                        "title": "2021-07(copy)",
                        "content": "..."
   }
}
```

其中 `file` 就是 base64 格式的内容，`content` 则包含了 pdf 的内容，如果不想要 `file` 则可以通过 `remove processor` 去除这个字段

```bash
PUT _ingest/pipeline/pdfattachment
{
  "description": "Extract attachment information encoded in Base64 with UTF-8 charset",
  "processors": [
    {
      "attachment": {
        "field": "file"
      }
    },
    {
      "remove": {
        "field": "file"
      }
    }
  ]
}
```

## 搜索

通过在 kibana 的开发工具执行下述命令，验证一下搜索：

```bash
GET pdf-test1/_search
{
  "query": {
    "match": {
      "attachment.content": "5G"
    }
  }
}
```

# 参考链接

[Elasticsearch：如何对PDF文件进行搜索](https://www.cnblogs.com/sanduzxcvbnm/p/12624935.html)