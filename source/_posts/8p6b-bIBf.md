---
title: '滴滴行程分享 Api'
date: 2021-09-25 16:38:40
tags: [爬虫,Python]
published: true
hideInList: false
feature: /post-images/8p6b-bIBf.jpeg
isTop: false
---
分享一个很久之前写的一个关于滴滴行程的脚本，脚本的主要功能是根据滴滴行程分享的 url 获取订单情况，通过定时轮询还可以获取乘客到目的地的距离和时间

```python
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
"""
@File    :   didi.py    
@Description：滴滴行程分享 Api
@Modify Time      @Author    @Version    @Description
------------      -------    --------    -----------
2021/5/29 8:47 上午   leetao      1.0         None
"""
import requests
import urllib.parse as urlparse
from urllib.parse import parse_qs
from dataclasses import dataclass, asdict

_header = {
    'Accept': 'application/json, text/plain, */*',
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.77 Safari/537.36',
    'Accept-Language': 'zh-CN,zh;q=0.9'
}


@dataclass
class DidiOrderParams:
    uid: str
    oid: str
    sign: str
    productType: str
    carProductid: str


def is_didi(url: str) -> bool:
    return url.startswith("https://z.didi.cn")


def get_order_params(url: str) -> DidiOrderParams:
    resp = requests.get(url, allow_redirects=False, headers=_header)
    if resp.status_code == 302:
        redirect_url = resp.headers['location']
        parsed = urlparse.urlparse(redirect_url)
        params = parse_qs(parsed.query)
        return DidiOrderParams(uid=params['uid'], oid=params['oid'], sign=params['sign'],
                               productType=params['productType'], carProductid=params['carProductid'])
    return None


def get_order_info(order_param: DidiOrderParams) -> str:
    resp = requests.get('https://common.diditaxi.com.cn/webapp/sharetrips/page/getOrderStatus',
                        params=asdict(order_param), headers=_header)
    assert resp.status_code == 200
    resp_json = resp.json()
    assert resp_json['errno'] == 0
    return f'本次行程 \n 起点：{resp_json["fromAddress"]} \n 终点: {resp_json["toAddress"]} \n 您的行程管家将定时为您播报行程情况'


def get_driver_loc(order_param: DidiOrderParams):
    resp = requests.get('https://common.diditaxi.com.cn/webapp/sharetrips/page/getDriverLoc',params=asdict(order_param),
                        headers=_header)
    assert resp.status_code == 200
    resp_json = resp.json()
    assert resp_json['errno'] == 0
    if 'etaDistance' not in resp_json:
        return f'本次行程已经结束'
    return f'距离目的地还有：{resp_json["etaDistance"]} 公里，预计还有：{resp_json["etaTime"]} 分钟'

```