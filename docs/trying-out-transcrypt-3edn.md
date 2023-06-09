# 试用抄本

> 原文：<https://dev.to/cathodion/trying-out-transcrypt-3edn>

对我来说没有意义的一点是，你不能在任何地方使用任何语言。特别是为什么不能用 Python 进行客户端 web 开发？

有人告诉我这是不现实的，原因有几个。提到了性能，因为任何其他语言都必须编译成 JavaScript，或者在实际运行脚本之前必须下载大型语言运行时(希望是 asm.js 或 WebAssembly)。另一个问题是可调试性:在其他语言中，您将无法在浏览器的开发工具中检查和进入源文件。Transcrypt 试图解决这两个问题。

## 什么是 Transcrypt？

[Transcrypt](https://www.transcrypt.org/) 是一个 Python 编译器，用 Python 编写，输出 JavaScript。标准的 Python 实现 CPython 有一些检查程序的[抽象语法树](https://en.wikipedia.org/wiki/Abstract_syntax_tree)的工具，Transcrypt 利用这些工具，以及 Google 的闭包编译器来生成 JavaScript 代码。它的输出是一个 JavaScript 文件，像任何其他 JavaScript 文件一样具有导出的函数和变量，并且能够访问外部 JavaScript 变量和函数。

Transcrypt 的目标是成为 Python 的一个“90%完整”的实现，速度优先于纯度。在我的测试项目中，我没有遇到任何“不完整”的事情。我确实遇到了一个很容易解决的小性能问题。就可调试性而言，Transcrypt 生成“sourcemaps”，它允许您使用浏览器的 devtools 进行调试。

## 我的测试项目

为了测试 Transcrypt，我将几年前编写的一个 JavaScript 程序移植到 Python 上。该程序使用 canvas 元素来绘制一束粒子，并模拟它们在重力作用下相互吸引的运动。因为每个粒子都会根据它们的距离和质量吸引其他粒子，所以这是一个 N^2 算法。效率不是很高，但是对于测试编译代码的性能来说，这并不是一件坏事。

```
# force of gravitation between pararticles p1 and p2,
# given their masses p1.m, p2.m and gravitational constant g def grav(p1, p2, g):
    r = dist(p1, p2)
    if (r == 0):
        r = 0.00000001
    return g * p1.m * p2.m / sq(r)

# apply force of gravitation to particle p1's velocity from p2,
#  given grav constant g def nudge(p1, p2, g):
    f = grav(p1, p2, g)
    theta = Math.atan2( (p2.y - p1.y), (p2.x - p1.x) )
    gravX = f * Math.cos(theta)
    gravY = f * Math.sin(theta)
    p1.vX += gravX / p1.m
    p1.vY += gravY / p1.m

    p1.vX, p1.vY = trimSpeed(p1.vX, p1.vY, MAX_SPEED)

# Let each particle pull on each other particle def gravParticles(g):
    for p1 in particles:
        for p2 in particles:
            if p1 is not p2:
                nudge(p1, p2, g) 
```

Enter fullscreen mode Exit fullscreen mode

[![Small equally sized red squares scattered on a white background.  The scattering is mostly random but there are a couple clusters and a few sparse places.](img/292c9b97d56d73b9a97a3e6fd03d37f5.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--D0cQobUq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i834vu6pyjlbxsdhr5xm.png) *你看不出来，但这些粒子都是相互吸引的。*

经过一番努力，安装了正确版本的 CPython(Transcrypt 网站说需要 3.6)，pip3 install transcrypt 工作顺利。

我的方法是从一个空的 python 文件开始，从现有的 JavaScript 文件中一次移动一个函数(修复缩进、括号等)。必要时)，并在每次重新编译后，确保程序仍然运行。这一切都很顺利，直到某个时候，事情变得不稳定。

## 表现

在 Python 和 JavaScript 文件之间来回移动各种片段之后，我确定问题出在这一行:

```
return { 'vX': vXNew, 'vY': vYNew } 
```

Enter fullscreen mode Exit fullscreen mode

因为它是我的“trimSpeed”函数的一部分，每对粒子(有 200 个)都要调用它，周期之间的预期时间是 50 毫秒(或 20 fps)，这意味着这段代码应该每秒调用近 200 * 200 * 20 = 800，000 次。缓慢的原因可能是 Transcrypt 创建的不仅仅是一个普通的 JavaScript 对象，而是一个 Python“[dict](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)”。

因为我在这里要做的只是返回速度的 X 和 Y 分量，所以我可以切换到更复杂的多重返回语句:

```
return vXNew, vYNew 
```

Enter fullscreen mode Exit fullscreen mode

这消除了起伏。

## 主循环

最后要移植的是我从 body 标签的`onload`属性调用的外部函数，以及我传递给`window.requestAnimationFrame()`的内部函数。内部功能基本就是程序的“[游戏循环](http://gameprogrammingpatterns.com/game-loop.html)”(减去用户输入和 AI 步骤)。`requestAnimationFrame`还向浏览器暗示，一些 GPU 加速会很好(这很好)。

这可以进行相当多的重构(更不用说我对全局变量不一致的滥用了)，但是我决定将剩下的部分全部移植进来:

```
def doit():
    canvas = document.getElementById('tutorial')
    CANVAS_WIDTH = canvas.width
    CANVAS_HEIGHT = canvas.height
    ctx = canvas.getContext('2d')

    init(particles, CANVAS_WIDTH, CANVAS_HEIGHT)

    last = None
    def go(timestamp):
        global last
        if not last:
            last = timestamp
        if timestamp - last >= 50:
            g = GRAVITATIONAL_CONSTANT
            gravParticles(g)
            moveParticles(CANVAS_WIDTH, CANVAS_HEIGHT)
            drawParticles(ctx, CANVAS_WIDTH, CANVAS_HEIGHT)
            last = timestamp

        # make this function keep looping
        window.requestAnimationFrame(go)

    # kick things off initially
    window.requestAnimationFrame(go) 
```

Enter fullscreen mode Exit fullscreen mode

```
...

<!-- the compiled python code -->
<script src="__javascript__/ent_tr.js"></script>

<!-- the original script (no longer used) -->
<!-- <script src="ent_tr_old.js"></script> -->
...
<body onload="ent_tr.doit();">
    <!-- yes, I evolved this from a 'hello world' canvas tutorial. don't judge me. -->
    <canvas id="tutorial" width="1000" height="500"></canvas>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

除了大括号、圆括号等的不同。这很可能是 JavaScript 代码。如您所见，用 Transcrypt 编译时，Python 代码中可以使用像`document`、`window`和`canvas`这样的 JavaScript APIs。

## 文件大小

它生成的文件有多大？对于 4 KB Python 源文件(`ent_tr.py`)，生成了以下文件:

*   ent_tr.js (77 KB)
*   ent_tr.mod.js (5 KB)
*   ent_tr.js.map (129 KB)
*   ent_tr.mod.js.map (5 KB)

我相信`ent_tr.js`包含 Transcrypt 运行时，所以在给定的应用程序中应该只需要有一个。`ent_tr.mod.js`，对应 ent_tr.py 文件本身所代表的模块，并不比其源 Python 文件大多少。*。映射文件是源映射，所以除非您正在调试，否则不需要下载它们。

## 外卖

我觉得这说明 Transcrypt 够快了。它似乎缺少的一点是在同一个文件中包含 HTML、CSS 和 Python 的能力，就像 JSX 和 React 一样。所以 Transcrypt 目前可能不是构建用户界面的最佳语言。一个用例是，如果您有(或计划创建)一个中等大小的 Python 库，并希望它可供其他客户端代码使用。如果你不想用相同的功能维护一个单独的 JavaScript 代码库，但希望它比你把它变成一个微服务时响应更快，Transcrypt 可能适合你。

我希望你觉得这很有趣。随着我继续试验 Transcrypt，我希望能写更多关于它的东西。你怎么想呢?80 KB 对于运行时来说太大了吗？我们应该用什么基准来确定它是否“足够快”？

一条鲜艳的蓝色蛇挂在一棵树上(画面外),前面是模糊的树叶背景。它黑色的大眼睛盯着摄像机。它有给你的礼物吗？