# 为什么没有编译器帮助使用日志冗长的东西

> 原文：<https://dev.to/xlebenny/why-no-compiler-help-use-log-verbose-thing-30oe>

通常，在用户报告有 bug 后，
我们得到日志，日志只有 2 件事

1.  `NullReferenceException`在第 x 行
2.  用户输入了什么数据

然后，我们需要手工制作一些测试用例或在 UI 中输入
可能需要 0.5 - 1 天来重现它
太麻烦了

为什么编译器没有给日志添加一些类似调试模式的`watch`功能

原件

```
var foo = "ABC"
var bar = "DEF"

foo = bar 
```

编译后

```
var foo = "ABC"
Console.WriteLine("foo = \"ABC\"")
var bar = "DEF"
Console.WriteLine("bar = \"DEF\"")

foo = bar
Console.WriteLine("foo = \"DEF\"") 
```

* * *

题外话:你在哪里找到一个帖子横幅图像