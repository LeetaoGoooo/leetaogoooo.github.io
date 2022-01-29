---
title: showDialog 中无法使用 setState()
tags: [Flutter]

date: 2020-08-16
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
showDialog 是 flutter 中一个展示 Material 风格对话框函数，正常情况下使用对话框，都是有相应对交互逻辑的，当我们需要改变某个属性的状态时，通常情况下会发现 **setState()** 无法使用。

# 场景
一般情况下我们使用 showDialog 的代码如下:
```dart
    showDialog(
      context: context,
      builder: (BuildContext context){
        return Container(
          child: Scaffold(
            body: Column(
              children: <Widget>[
                ... // 省略代码
              ],
            ),
          ),
        );
      }
    );
```
在这种情况下如果使用 **setState() **会发现**通过 setState() 更新的属性不会生效**。

# 解决
如何解决这个问题呢？有两个解决办法。在 dialog 中声明一个 [StatefulBuilder](https://api.flutter.dev/flutter/widgets/StatefulBuilder-class.html) 或者 [StatefulWidget](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html)。可以看一下具体的例子：


### StatefulBuilder 样例
```dart
await showDialog<void>(
  context: context,
  builder: (BuildContext context) {
    int selectedRadio = 0;
    return AlertDialog(
      content: StatefulBuilder( // 重点
        builder: (BuildContext context, StateSetter setState) { // 重点
          return Column(
            mainAxisSize: MainAxisSize.min,
            children: List<Widget>.generate(4, (int index) {
              return Radio<int>(
                value: index,
                groupValue: selectedRadio,
                onChanged: (int value) {
                  setState(() => selectedRadio = value);
                },
              );
            }),
          );
        },
      ),
    );
  },
);
```
重点在6，7行代码

### StatefulWidget 样例
自定义一个 StatuefulWidget 的组件
```dart
class Bird extends StatefulWidget {
  const Bird({
    Key key,
    this.color = const Color(0xFFFFE306),
    this.child,
  }) : super(key: key);

  final Color color;
  final Widget child;

  _BirdState createState() => _BirdState();
}

class _BirdState extends State<Bird> {
  double _size = 1.0;

  void grow() {
    setState(() { _size += 0.1; });
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      color: widget.color,
      transform: Matrix4.diagonal3Values(_size, _size, 1.0),
      child: widget.child,
    );
  }
}
```

# 参考
[Flutter: setState on showDialog()](https://stackoverflow.com/questions/58977815/flutter-setstate-on-showdialog)

