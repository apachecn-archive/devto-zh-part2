# 这是我的梦想语言。是真的吗？

> 原文：<https://dev.to/nektro/this-is-my-dream-language-is-it-real-3no1>

有一段时间，我对自己的“梦想”语言有了一个愿景。((抱歉 [@mortoray](https://dev.to/mortoray) ，我觉得不是叶子，还是爱你的内容))

我想出的语法受 JavaScript 启发，但增加了一些天赋，使它(最好的情况)与 ASM 和 WASM 编译兼容。我在下面留下了一些示例代码，我想知道是否有人知道是否存在类似下面代码片段的语言。

```
import { sqrt, pow } from "math"

export class Point {
    struct {
        Int x,
        Int y
    }
    constructor(Int a, Int b) {
        this.x = a;
        this.y = b;
    }
    constructor(Int a) {
        this(a, a);
    }
    constructor() {
        this(0, 0);
    }
    distanceTo(Point pt) -> Float {
        return sqrt(pow(pt.x - this.x, 2) + pow(pt.y - this.y, 2));
    }
}

export class Circle {
    struct {
        Point center,
        Int radius
    }
    constructor(Point c, Int r) {
        this.center = c;
        this.radius = r;
    }
    constructor(Int r) {
        this(new Point(0, 0), r);
    }
    constructor() {
        this(1);
    }
    intersects(Point pt) -> Boolean {
        return pt.distanceTo(this.center) <= this.radius;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

提前感谢任何帮助！

* * *

事后看来，这看起来很像一个真正的类似 Java-Script 的语法，但我仍然很好奇，在我出去尝试开始为一个已经在做的项目制作编译器之前，是否有人知道这是否存在:)