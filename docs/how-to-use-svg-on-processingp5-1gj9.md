# 如何在处理中使用 SVG(P5)

> 原文：<https://dev.to/toyotarone/how-to-use-svg-on-processingp5-1gj9>

首先，我的示例程序是这里的。

# 先决条件

您已经在 PC/Mac 上安装了[处理](https://processing.org/)。
也请得到一个 SVG 文件显示。

# 如何

首先，你要准备`PShape`物体。
之后，调用`loadShape`方法。

```
PShape some_kind_of_shape;
some_kind_of_shape = loadShape("Some_Kind_of_Shape.svg"); 
```

Enter fullscreen mode Exit fullscreen mode

实际上，为了简单起见，我在示例代码中将 PShape 变量定义为 static。

当你准备好对象后，只需调用`draw`方法中的`shape`方法，你就可以看到你的 svg 了。

```
void draw() {
  background(255);
  shape(some_kind_of_shape, position_x, position_y, shape_width, shape_height);
} 
```

Enter fullscreen mode Exit fullscreen mode