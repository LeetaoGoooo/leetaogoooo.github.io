---
title: 使用flutter创建macOS桌面软件常见错误(持续更新)
tags: [Flutter]

date: 2020-03-21
published: true
hideInList: false
feature: 
isTop: false
---



# 前言

Flutter 在桌面软件端还需要走很长的路～跨平台支持的通用插件是在是太少了...

# 问题列表

1.
```
ProcessException: Process exited abnormally:
xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer
directory '/Library/Developer/CommandLineTools' is a command line tools instance
  Command: /usr/bin/xcodebuild -list -project Runner.xcodeproj
```
  
## 解决办法

打开命令行，再命令行输出以下命令：

```shell
xcode-select --install # 如果没有安装 Command Line Tools 的，需要执行这条命令
sudo xcode-select --switch /Library/Developer/CommandLineTools # 启动 Command Line Tools
```
可能会出现warning信息，可以忽略。如果你安装了 Xcode 的话，可以再执行下面的命令：

```shell
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer # 后面是 Xcode 的路径，默认路径是这个
```


2.

```
The argument type 'MyState' can't be assigned to the parameter type 'TickerProvider'.
```
## 解决办法

在State类的末尾添加 with TickerProviderStateMixin

```dart
class _MyHomePageState extends State<MyHomePage> with TickerProviderStateMixin{}
```
