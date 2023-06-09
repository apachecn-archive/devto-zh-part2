# 处理幻值和常量的更好方法？

> 原文：<https://dev.to/mjb2kmn/a-better-way-to-handle-magic-values-and-constants-2o4g>

众所周知，代码中的“神奇值”是个坏主意，应该给它起一个描述性的名字。与其这样:

```
throw new Exception("invalid state: 0x02") 
```

Enter fullscreen mode Exit fullscreen mode

最好将“幻值”定义为常量:

```
Integer STATE_OK = 0x01;
Integer STATE_BAD = 0x02;
...
throw new Exception ("invalid state: {STATE_BAD}") 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，这种方式运行良好。然而，无论哪种情况，用户看到的都是:

```
$ invalid state: 02 
```

Enter fullscreen mode Exit fullscreen mode

我在这里想到的是为技术用户设计的框架和工具，而不是为非技术终端用户设计的。

在我的实际用例中，我能够找到源代码并搜索出所报告值的常量名称来发现实际问题，但这相当不方便，并且不总是可能的，比如在使用封闭源代码工具或用户不熟悉给定语言时。

作为一名开发人员，我对如何改善这种体验很感兴趣。从表面上看，字符串值似乎是一种改进，但这显然不能很好地扩展。这仅仅是一个文档失败，没有更好的方法在代码中处理它吗？见过完全不同的方法吗？