# 使用 HTML5 的扫雷工具

> 原文：<https://dev.to/aymanbagabas/minesweeper-using-html5-4goj>

显然，2000 年之前出生的人都知道著名的经典游戏‘扫雷’。对我来说，我很小的时候就知道这个游戏，那时 Windows XP 无处不在。这很有趣，因为那时我并不知道这个游戏到底是怎么玩的。那次游戏对我来说是一种运气游戏，你要努力消除除了有地雷的方块之外的所有方块，直到你赢或输😆。

直到最近，一个朋友指出，他们看到了一个家伙制作了一个完全完美的 AI 来解决游戏的视频。我喜欢这个主意，所以我决定做一个。我开始学习这个游戏是如何工作的，结果非常简单。游戏中有一定数量的地雷，玩家必须找到这些地雷的位置才能赢得游戏。每个方块都有一个权重，显示在该特定方块周围的 8 个方块中有多少地雷。如果权重为零，游戏将显示所有周围的方块，除了有地雷的方块，否则，它将显示方块本身。虽然事情并不顺利，但我最终只做了这个游戏。

### 它是如何工作的？

借助 HTML5 canvas 元素，可以绘制几何对象。我们可以使用 JavaScript 动态地实现这一点。首先，我们在页面中创建一个画布元素:

```
<canvas id="example" width="500" height="500">Any text here will get displayed if the browser does not support HTML5 canvas</canvas> 
```

Enter fullscreen mode Exit fullscreen mode

然后我们用 JavaScript 创建对象并控制它们的属性:

```
var canvas = document.getElementById("example");
var context = canvas.getContext("2d");
context.beginPath();
context.fillStyle = 'red';
context.fillRect(0, 0, 10, 10);
context.closePath(); 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个边长等于 10、位置为(0，0)的正方形。简单易行。

### 蛴螬

我使用 [Grub](https://wiki.archlinux.org/index.php/GRUB) 作为引导装载程序。显然，您希望将 Grub 用于 UEFI 系统。对于 ESP 位置，我将我的设置为`/boot/efi`,只是为了遵循其他 Linux distors 方法。由于这款笔记本电脑自带的 HiDPI 屏幕，Grub 很难看到，一个快速的解决方法是将`GRUB_GFXMODE`变量设置为类似于`1600x1200x32`的值。可以通过执行`videoinfo`从 Grub 命令行获取可用的值。编辑您的`/etc/default/grub`文件，以包含这些行:

### 扫雷舰

出于面向对象编程的心态，我希望能够创建类，因为我已经习惯了这种方式😃。当我知道你可以用 JavaScript 模仿创建[类时，我很惊讶。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)

我开始问每个方块有什么属性？我想到了这些:isMine，isFlagged，isDown，x，y 和 weight。isMine 判断一个方块是不是地雷。当一个方块被标记或标示时。如果显示正方形，则为 Down。x 和 y 代表游戏中的位置。重量是一个大于零的数字，表示它周围的方格中有多少地雷。有了这些属性，我就有了这个 JS 类:

```
 1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34

class square {
    constructor(x, y) {
        this.x = x;
        this.y = y;
        this.isDown = false;
        this.isMine = false;
        this.isDown = false;
        this.weight = 0;
    }
    get x() { return this._x; }
    set x(value) { this._x = value; }
    get y() { return this._y; }
    set y(value) { this._y = value; }
    get weight() { return this._weight; }
    set weight(value) { this._weight = value; }
    get isDown() { return this._isDown; }
    set isDown(value) {
        this._isDown = value;
        if (this.isMine) {
            end = true;
        }
    }
    get isMine() { return this._isMine; }
    set isMine(value) { this._isMine = value; }
    get isFlagged() { return this._isFlagged; }
    set isFlagged(value) {
        this._isFlagged = value;
        if (value)
            numberofmines = (numberofmines <= 0) ? 0: numberofmines - 1;
        else
            numberofmines++;
        updateHeader();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个游戏没有循环，它包含两个主要功能。第一个被调用来初始化游戏环境，就像设置鼠标事件处理程序一样。这里是所有神奇的事情发生的地方，每当用户点击第一次点击时，计时器开始计时，并将点击传递给游戏。如果它在方块所在的游戏框架内，它将记录该方块的点击。否则，它将开始新的游戏。也就是第二个函数，前一个函数只被调用一次。这个被调用来清除游戏中所有以前的数据并创建一个新的游戏。显然，这是在其他函数的帮助下完成的，以使它更有组织性。

### 源代码

源代码可以在[js minesweaver](https://github.com/aymanbagabas/jsminesweeper)获得，或者你可以在这里[试用。](https://aymanbagabas.com/jsminesweeper)