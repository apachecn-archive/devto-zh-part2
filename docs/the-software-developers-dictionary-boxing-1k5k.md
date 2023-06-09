# 软件开发人员的字典装箱

> 原文：<https://dev.to/calvinallen/the-software-developers-dictionary-boxing-1k5k>

对于我们的第一个字典条目，我们将看看这个术语，“拳击”。

装箱可以描述为转换值类型(整数、小数、字符等)的过程。)到`object`类型，或者到值类型实现的任何接口类型。

“装箱”行为是隐式的(也可以显式装箱，但不是必需的)，而“取消装箱”行为是显式的。

看一下下面的示例代码，它将`unboxedInt`打包成对象`boxedInt` :

```
int unboxedInt = Int32.Max;

object boxedInt = unboxedInt; 
```

Enter fullscreen mode Exit fullscreen mode

装箱是一个相当昂贵的过程，因为在堆栈上创建了一个新对象`boxedInt`，然后它引用堆上的`unboxedInt`值的副本(这允许垃圾收集)。