# 如何给 Flutter 的任何小部件添加一个点击事件

> 原文：<https://dev.to/rkowase/how-to-add-a-click-event-to-any-widget-of-flutter-2len>

如何给 Flutter 的任何小部件添加一个点击事件？

## TL；速度三角形定位法(dead reckoning)

使用`GestureDetector`小工具

## 样本代码

```
GestureDetector(
  onTap: () {
    print("onTap called.");
  },
  child: Text("foo"),
), 
```

Enter fullscreen mode Exit fullscreen mode

## 环境

*   颤动 0.1.5
*   dart 2 . 0 . 0-开发版 28.0

## 链接

用户界面-颤动-容器？-堆栈溢出
[https://Stack Overflow . com/questions/43692923/flutter-container-on pressed](https://stackoverflow.com/questions/43692923/flutter-container-onpressed)