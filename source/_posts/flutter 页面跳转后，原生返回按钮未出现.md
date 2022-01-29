---
title: flutter 页面跳转后，原生返回按钮未出现
tags: [Flutter]

date: 2020-08-18
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
Flutter 开发 App 的时候有一些比较方便特性，比如这次的主题：**跳转页面后 AppBar 会自带返回按钮**。当然想要出现返回按钮是需要前提的。

# 使用方法
主页面通常会使用如下代码：
```dart
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
            home: MyHomePage());
  }
}
```
如果想跳转到其他页面，其他页面出现返回按钮的话，在其他页面就不需要使用 **MaterialApp** 了，直接使用 **Scaffold**
```dart
class OtherPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return  Scaffold(
      body: ConfigPageStatefulWidget(),
    );
  }
}
```


