---
title: 'Docker 搭建 Elasticsearch + Kibana + IK 中文分词'
date: 2021-09-07 18:26:33
tags: [Elasticsearch]
published: true
hideInList: false
feature: /post-images/Dxqjjq53O.png
isTop: false
---
# 安装启动 Elasticsearch

```docker
docker network create elastic
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.14.0
docker run -d --name es01-test --net elastic -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.14.0
```

# 安装启动 Kibana

```docker
docker pull docker.elastic.co/kibana/kibana:7.14.0
docker run -d --name kib01-test --net elastic -p 5601:5601 -e "ELASTICSEARCH_HOSTS=http://es01-test:9200" docker.elastic.co/kibana/kibana:7.14.0
```

启动完成之后访问 [`http://localhost:5601`](http://localhost:5601/) 即可，默认是英文，配置中文可以参考

## Kibana 汉化

在 `kibana.yml` 中加入

```java
i18n.locale: "zh-CN"
```

然后重启 kibana 即可

配置完成后重启服务，界面如下图：

![](https://www.leetao94.cn/post-images/1631010579381.png)

# 安装 IK 中文分词

es 中本身自带英文分词，但是实际业务需要存在中文分词的场景，所以手动安装中文分词插件。

## 下载 IK 分词插件

从 github 上根据不同的 es 版本下载对应的 ik 插件压缩包

[https://github.com/medcl/elasticsearch-analysis-ik/releases](https://github.com/medcl/elasticsearch-analysis-ik/releases)

## 在 es 的 plugins 解压

```docker
cd your-es-plugins/plugins/ && mkdir ik
unzip elasticsearch-analysis-ik-7.14.0.zip
```

然后重新启动 es，这里我是使用 docker 启动 es 的，所以先通过 `docker cp` 的命令将插件复制到容器中的

```docker
docker cp es.zip container-id:/usr/share/elasticsearch/plugins/es
```

# 重启 es

重启 es 的服务，如果成功加载插件，控制台会有如下的输出:

![](https://www.leetao94.cn/post-images/1631010619965.png)

