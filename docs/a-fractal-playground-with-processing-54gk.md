# 具有处理功能的分形操场

> 原文：<https://dev.to/zanehelton/a-fractal-playground-with-processing-54gk>

Sooo [处理](https://processing.org/)相当酷。当[丹尼尔·希夫曼](https://www.youtube.com/channel/UCvjgXvBlbQiydffZU7m1_aw)在我的 YouTube feed 上被推荐时，我第一次遇到了它，从那以后，我从它身上获得了很多乐趣。简而言之:这是一个 Java sketchbook，允许任何人在 2D / 3D 空间中创建交互式和可视化的草图。它超级强大，而且超级容易上手。

[![](img/ae6df4c8afd44c209b8274672d929e41.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B1-gS7ck--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yxnirigrlwwve6xz2j71.png)

上面是一个只用几行代码创建的基本绘图程序。处理还有很多可以提供的，但是在这篇文章中，我将带你从简单的形状中制作分形。

但是在我们开始疯狂地搜索 StackOverflow 寻找 Java 编译器错误之前，让我们先来谈谈分形是如何形成的。

[![](img/165879169a4b633bdc8269c95c99ae7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FbppVbYX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fvho3vpz5g3fv6fq2bwa.gif)T3】

<center>Triangle de Sierpinski</center>

分形是递归驱动的简单模式(不要惊慌)。
考虑递归图像:

[![](img/a4db94f9e13030515e6eff879a539c88.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u9n3rEMf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wg4a9c1krq5iatf6jd7b.png)

花一点时间想想你可以实现这样的东西的方法。什么决定了序列中的下一步？怎样才能保证方块不重叠？

一旦你想好怎么做，就扔掉它，因为除非你说“每一步都移动原点”，否则这可能会是一场争论。递归函数在处理过程中的诀窍是跟踪你的原点。这是之前的同一个草图，但是在每次迭代中都有一个红色的圆圈跟随原点。

[![](img/8e9171a9486b902657c49027f43708b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--30kyHxrl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mxv27n9ucou2dbihbplj.png)

在加工过程中，移动原点的函数是`translate(...)`，而`translate(...)`是相对于原点之前的位置移动原点的函数。例如:`translate(3, 9)`会将原点向右移动 3，向下移动 9。

## 咱们码吧

之前的缩放方块的例子是一个合理的第一步，所以我们将从这里开始。

```
void setup() {
  size(1000, 250);
}

void draw() {
  scalingSquares(200); // we'll define this function next
                       // it'll be called once per frame
} 
```

Enter fullscreen mode Exit fullscreen mode

这两个功能是你的加工草图的心脏和灵魂，所以尽量保持干净。

我们将把负责绘制方块的代码放到它自己的函数`scalingSquares(...)`中。

```
void scalingSquares(float n) {
  rect(0, 0, n, n);
  translate(n + 5, n / 8);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您尝试将 200 作为`draw()`中的参数，将会看到:

[![](img/928ea7bbf82592c6801b4edba49acb4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SgiwQLeN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4thq0swf5j0rurkengt2.png)

除了关键成分，我们已经有了完成缩放方块所需的一切...递归！

通过添加这两行...

```
void scalingSquares(float n) { + if (n < 30) return;       // don't draw squares that are less than 30x30
  rect(0, 0, n, n);         //
  translate(n + 5, n / 8);  // offset each square by itself + 5px
                            // and move each square down 1/8 to vertically align
                            //
+ scalingSquares(n * 0.75); // each square is 75% the size of the one before it } 
```

Enter fullscreen mode Exit fullscreen mode

我们已经用递归函数实现了缩放正方形的功能。

[![](img/4af5610cf8d228de65bc91bd81f7bd81.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4ufY_DJz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pfbdd0t1kem12j6bbhlj.png)

需要注意的一件重要事情是在`scalingSquares(...)`中传递给`scalingSquares(...)`的值。它是原始参数乘以 0.75，以减少进入递归函数的比例因子，直到它小于 30 像素。

## 更好的分形？

如果你在互联网上呆过一段时间，你就会知道这几乎不是分形。为了创造更复杂的分形，将`pushMatrix()`和`popMatrix()`加入你的武器库将是明智的。`pushMatrix()`将当前原点和网格属性保存到离屏堆栈中，而`popMatrix()`将原点和网格恢复到最近存储的状态。将`pushMatrix()`视为保存功能，将`popMatrix()`视为网格的加载功能。

```
void setup() {
  size(700, 700);
}

void draw() {
  background(255);

  noFill();

  translate(width / 2, height / 2); // move origin to middle of canvas
  draw_fractal(500);
}

void draw_fractal(float r) {
  ellipse(0, 0, r, r);  // draw origin circle

  if (r < 100)          // don't draw circles smaller than 100x100
    return;

  stroke(255, 0, 0);    // red circles
  pushMatrix();         // save the origin, we're about to change it
  translate(0, -r / 4); // move the origin straight down to the 1/4 mark
  draw_fractal(r / 2);  // restart the process at half the scale
  popMatrix();

  stroke(0, 0, 255);    // blue circles
  pushMatrix();
  translate(0, r / 4);
  draw_fractal(r / 2);
  popMatrix();

  stroke(0, 255, 0);    // green circles
  pushMatrix();
  translate(r / 4, 0);
  draw_fractal(r / 2);
  popMatrix();

  stroke(0);            // black circles
  pushMatrix();
  translate(-r / 4, 0);
  draw_fractal(r / 2);
  popMatrix();
} 
```

Enter fullscreen mode Exit fullscreen mode

这段递归代码有如下输出:
[![](img/8a884b243ff2d9182e0e67211774ce23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bmA0PxTV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qk29xr7tur3nkvvm5kjq.png)

红色:顶圈
蓝色:底圈
绿色:右圈
黑色:左圈

带原点标记:
[![](img/0d2d85c9f763e16e4de9b4a6eeec69fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gcq_3t_6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qlx426zgak3gs4zvo8kl.png)

## 结论

我希望这是一本有趣的读物，也是对代码可视化的一个很好的介绍。处理不仅仅适用于不稳定的分形，它也是一种工具，可以用很酷的新方法可视化数据。网上有很多免费的分形片段可以处理，但是如果你想出了一些很酷的东西，你有动力去创建你自己的并在这里分享它们！为了增加更多的颜色，对`strokeWeight(...)`、`stroke(...)`、`noFill()`、`fill(...)`和`background(...)`进行调整可能会很有趣。如需更多文档，请查看 [Processing 的网站](https://processing.org/reference/)。