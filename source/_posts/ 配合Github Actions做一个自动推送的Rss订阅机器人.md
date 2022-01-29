---
title:  配合Github Actions 做一个自动推送的 Rss 订阅机器人
tags: [Python,Github Action]

date: 2020-10-11
published: true
hideInList: false
feature: 
isTop: false
---

# 前言

一直都有订阅 Rss 的习惯，前段时间配合钉钉的自定义机器人，建了一个在钉钉定时推送消息的群，当时是服务是跑在自己的小水管上的，并且是在自己博客的基础上的，依赖了 Flask。后续想着开源，考虑到单独一个 Rss 推送机器人依赖一个 Web 框架就显得比较笨重了，就着手将这部分服务独立出来。

# 钉钉 RSS 推送机器人

钉钉 RSS 推送机器人，主要内容分为两部分，第一部分是钉钉机器人；第二部分是订阅推送。

## 钉钉机器人

因为是自定义内容，钉钉现有的机器人不支持，所以我们需要借助钉钉的自定义机器人。

### 步骤一
【电脑钉钉 】-【群聊】-【群设置】-【智能群助手】-【添加更多】-【添加机器人】-【自定义】-【添加】，编辑机器人名称和选择添加的群组。完成必要的安全设置（至少选择一种），勾选 我已阅读并同意《自定义机器人服务及免责条款》，点击“完成”即可。
![](/posts/1603444780.png)


### 步骤二
复制出机器人的Webhook地址（格式如下截图），可用于向这个群发送消息。并点击【完成】，自定义机器人就添加成功了。
![](/posts/1603444871.png)
为了安全考虑，这里我们在创建机器人的时候，勾选“**加签**”。这样子会获得 webhook 和 secret 两个参数

```python
webhook = 'https://oapi.dingtalk.com/robot/send?access_token=这里填写自己钉钉群自定义机器人的token'
secret = 'SEC11b9...这里填写自己的加密设置密钥'
```

## 消息推送

完成机器人的创建，就需要完成通过 api 将消息推送到群里，使用 [DingtalkChatbot](https://github.com/zhuifengshen/DingtalkChatbot)

### 安装
```bash
pip install DingtalkChatbot
```

### 例子
```python
from dingtalkchatbot.chatbot import DingtalkChatbot
# WebHook地址
webhook = 'https://oapi.dingtalk.com/robot/send?access_token=这里填写自己钉钉群自定义机器人的token'
secret = 'SEC11b9...这里填写自己的加密设置密钥'  # 可选：创建机器人勾选“加签”选项时使用
# 初始化机器人小丁
xiaoding = DingtalkChatbot(webhook)  # 方式一：通常初始化方式
xiaoding = DingtalkChatbot(webhook, secret=secret)  # 方式二：勾选“加签”选项时使用（v1.5以上新功能）
xiaoding = DingtalkChatbot(webhook, pc_slide=True)  # 方式三：设置消息链接在PC端侧边栏打开（v1.5以上新功能）
# Text消息@所有人
xiaoding.send_text(msg='我就是小丁，小丁就是我！', is_at_all=True)
```

## RSS 解析
rss 解析，这里使用 feedparser 进行解析。

### 安装
```bash
pip install DingtalkChatbot
```

### 例子
```python
>>> import feedparser
>>> leetao_feed = feedparser.parse('https://www.leetao94.cn/feeds') # parse() 方法，解析对应的 url
>>> leetao_feed.feed # feed 对象
{'title': "Peach Blog's Feeds", 'title_detail': {'type': 'text/plain', 'language': None, 
                                                 'base': 'https://www.leetao94.cn/feeds', 'value': "Peach Blog's Feeds"}, 
 'id': 'https://www.leetao94.cn/feeds', 'guidislink': True, 
 'link': 'https://www.leetao94.cn/', 'updated': '2020-09-25T19:53:35Z',
 'updated_parsed': time.struct_time(tm_year=2020, tm_mon=9, tm_mday=25, tm_hour=19, tm_min=53, tm_sec=35, tm_wday=4, tm_yday=269, tm_isdst=0), 
 'links': [{'href': 'https://www.leetao94.cn/', 'rel': 'alternate', 'type': 'text/html'}, 
           {'href': 'https://www.leetao94.cn/feeds', 'rel': 'self', 'type': 'application/atom+xml'}], 
 'generator_detail': {'name': 'Werkzeug'}, 'generator': 'Werkzeug'}
>>> type(leetao_feed.entries) # 解析出来的文章列表对象
<class 'list'>
>>> len(leetao_feed.entries)
10
>>> leetao_feed.entries[0] # 单个文章的内容
{'title': 'Mybatis中特殊符号#{}和${}', 
 'title_detail': {'type': 'text/plain', 
                  'language': None, 'base': 'https://www.leetao94.cn/feeds',
                  'value': 'Mybatis中特殊符号#{}和${}'},
 'id': 'https://www.leetao94.cn/post/Mybatis中特殊符号#{}和${}', 
 'guidislink': True, 'link': 'https://www.leetao94.cn/post/Mybatis中特殊符号#{}和${}',
 'updated': '2020-09-25T19:53:35Z', 
 'updated_parsed': time.struct_time(tm_year=2020, tm_mon=9, tm_mday=25, tm_hour=19, tm_min=53, tm_sec=35, tm_wday=4, tm_yday=269, tm_isdst=0),
 'published': '2020-09-25T19:53:35Z', 
 'published_parsed': time.struct_time(tm_year=2020, tm_mon=9, tm_mday=25, tm_hour=19, tm_min=53, tm_sec=35, tm_wday=4, tm_yday=269, tm_isdst=0),
 'links': [{'href': 'https://www.leetao94.cn/post/Mybatis中特殊符号#{}和${}', 
            'rel': 'alternate', 'type': 'text/html'}], 'authors': [{'name': 'Leetao'}], 
 'author_detail': {'name': 'Leetao'}, 'author': 'Leetao', 
 'content': [{'type': 'text/html', 'language': None, 'base': 'https://www.leetao94.cn/feeds',
              'value': '...'}
```
具体用法可以参考官方文档：[https://pythonhosted.org/feedparser/](https://pythonhosted.org/feedparser/)

## RSS 钉钉机器人
准备工作完成之后，就是将机器人和 Rss 解析内容整合到一起，为了避免重复推送内容，很显然需要借助数据库，考虑简便性，采用 python 原生支持的 **sqlite 数据库**免安装；为了操作数据库方便，这里就使用轻量级的 ORM -- **peewee**。


### 模型定义
首先完成数据模型的定义，两个模型，一个是 RSS 源模型，一个是 History 历史记录模型。
```python
# models.py
# -*- encoding: utf-8 -*-
from peewee import *
import datetime

db = SqliteDatabase('rss.db')


class BaseModel(Model):
    class Meta:
        database = db


class Rss(BaseModel):
    feed = CharField(unique=True) # rss 源地址
    cover = CharField(max_length=255) # 封面 
    title = CharField(max_length=20) # rss 的标题
    url = CharField(max_length=255) # 官网地址 可选


class History(BaseModel):
    url = CharField(max_length=255)
    publish_at = DateField(default=datetime.datetime.now)


def create_tables():
    with db:
        db.create_tables([Rss, History])
```
模型定义完成，接下来就是代码的整合了。

### 整合代码
```python
# -*- encoding: utf-8 -*-

"""
rss.py
钉钉 Rss 机器人
 
"""
from datetime import datetime
import feedparser
from dingtalkchatbot.chatbot import DingtalkChatbot, CardItem, ActionCard
import dateparser
from models import db, Rss, History
import os

class RssRobot:
    def __init__(self):
        self.robot = DingtalkChatbot(
            os.environ.get("DD_WEBHOOK"),
            pc_slide=True, secret=os.environ.get("DD_SECRET")) # 两个环境变量

    def parse_rss(self):
        rss_list = Rss.select()
        rss_card_dict = {}
        post_url_list = [rss_history.url for rss_history in
                         History.select().where(History.publish_at == datetime.today().strftime("%Y-%m-%d"))] # 查询当天的历史记录
        for rss in rss_list:
            rss_history_list = []
            card_list = [
                CardItem(title=rss.title, url=rss.url, pic_url=rss.cover)]
            feed = feedparser.parse(rss.feed)            
            for entry in feed.entries:
                if entry.link not in post_url_list and self.is_today(entry): # 判断链接是否是当天的并且没有推送过的
                    card_list.append(CardItem(title=f'{entry.title}', url=entry.link,
                                            pic_url='https://ftp.bmp.ovh/imgs/2020/07/6cdb9f606677c9e3.jpg'))
                    rss_history_list.append(History(url=entry.link))

            if len(card_list) > 1:
                rss_card_dict[rss.title] = card_list
                with db.atomic():
                    History.bulk_create(rss_history_list, batch_size=10)

        return rss_card_dict

    def is_today(self, entry):
        return dateparser.parse(entry['updated']).date() == datetime.today().date()

    def send_rss(self):
        rss_card_dict = self.parse_rss()
        for key in rss_card_dict:
            self.robot.send_feed_card(rss_card_dict[key])


def send_rss():
    rss_bot = RssRobot()
    rss_bot.send_rss()

if __name__ == '__main__':
    send_rss()
```
到这里，算是完成了 Rss 钉钉机器人的大部分功能了，还差唯一的定时推送，原本考虑是用 Python 的常见的定时任务库去完成的，但是考虑的后续部署，还需要一台服务器，奈何自己的阿里云服务器最低配不一定能支持住了，就想着结合 Github Actions 把服务器也省了。

# Github Actions

`Github Actions`是`Github`推出的一个新的功能，可以为我们的项目自动化地构建工作流，例如代码检查，自动化打包，测试，发布版本等等。入口在项目`pull request`的旁边。

![](/posts/1603444919.png)
这里就不再详细介绍 Github Actions 了，贴一下我写的 workflows 文件

## workflows
```yaml
# 钉钉 rss 推送机器人
name: DingDingRssBot

on:               
  schedule:                              # 设置定时任务
    - cron: '0 0/1 * * *'
    
jobs:
  build:

    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout codes'
      uses: actions/checkout@v2           # 拉取最新的代码
    - name: Set up Python 3.7.6
      uses: actions/setup-python@v2       # 设置 python 版本
      with:
        python-version: 3.7.6
    - name: Install dependencies          # 安装依赖
      run: |
        cd robot
        pip install -r requirements.txt
    - name: send rss                      
      env:
        DD_WEBHOOK: ${{ secrets.webhook }}
        DD_SECRET: ${{ secrets.secret }}
      run: |
        cd robot
        python rss.py
    - name: Commit
      run: |
        git config --global user.email leetao94cn@gmail.com
        git config --global user.name lt94
        git add .
        git commit -m "update" -a
    - name: Push changes
      uses: ad-m/github-push-action@master 
      with:
        branch: main
        github_token: ${{ secrets.TOKEN }}
```
其中三个环境变量需要我们自行配置，在项目的 Settings 中的 Secrets 中设置
![](/posts/1603444956.png)

其中 TOKEN 是 Person access tokens，需要在个人设置中申请，申请时需要勾选 **admin:repo_hook,repo,workflow** 三个选项。

![](/posts/1603444990.png)

Person access tokens 主要是用来将修改推送到仓库的，借助 Github Actions 运行完成之后会把代码删除，会导致产生的修改丢失，这样子的话历史记录就无法保存，因此我们需要在每次推送完成之后，将对数据库的修改推送到仓库中。

# 总结
到此所有配置完成，Github Actions 会定时执行 rss 推送任务。
![](/posts/1603445024.png)

有兴趣的可以用钉钉搜索群号:35731271，或者扫描二维码

![](/posts/1603445048.png)


