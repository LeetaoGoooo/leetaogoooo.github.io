---
title: HttpClient 默认不使用代理【Dart 】
tags: [Flutter,dart]

date: 2021-02-01
published: true
hideInList: false
feature: 
isTop: false
---



# 前言


最近使用 flutter 构建 App，涉及到网络请求部分，使用了 dart 自带的 HttpClient 库发现了一个有趣的问题，dart 默认情况下不使用代理，即使电脑开着代理。
# 解决方案
HttpClient 有个 [findProxy ](https://api.dart.dev/stable/2.10.5/dart-io/HttpClient/findProxy.html)方法，复写这个方法就是设置代理，因此只需要在请求之前设置代理就可以了。
## findProxy
```dart
HttpClient client = HttpClient();
client.findProxy = (uri) {
  return "PROXY localhost:3128;";
};
```
但是显然在开发过程中我们并不清楚代理具体的 ip 和端口，所以为了提高可用性，需要一个方法帮助我们发现系统代理。
## 发现系统代理
flutter 有现成的三方 [package-system_proxy](https://github.com/kaivean/system_proxy) 可以帮助获取代理。
### 安装 system-proxy
在 pubspec.yaml 中添加依赖
```yaml
system_proxy: ^0.0.1
```
### 使用
```dart
Map<String, String> proxy = await SystemProxy.getProxySettings();
if (proxy != null) {
    print('proxy $proxy');
}
```
## 优化代码
成功安装完需要的 package 之后，优化一下代码，使得在请求之前使用系统代理。
```dart
extension HttpClientExtension on HttpClient {
  Future<HttpClient> autoProxy() async {
    Map<String, String> sysProxy = await SystemProxy.getProxySettings();
    var proxy = "DIRECT";
    if (sysProxy != null) {
      proxy = "PROXY ${sysProxy['host']}:${sysProxy['port']}; DIRECT";
      print("find proxy $proxy");
    }
    this.findProxy = (uri) {
      return proxy;
    };
    return this;
  }
}

/// 使用方法
 var httpClient = await new HttpClient().autoProxy();
/// 逻辑
```


# 参考链接
[Network calls don't show in Charles Proxy Debugger](https://github.com/flutter/flutter/issues/20376)

[Support proxy configuration in HttpClient](https://github.com/dart-lang/sdk/issues/5468)


