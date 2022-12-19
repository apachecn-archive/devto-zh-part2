# 快速 CSS 测验#5

> 原文：<https://dev.to/ismail9k/quick-css-quiz-5-3gd5>

在这个系列中，我试图将重点放在 CSS 怪异的部分，抛出 quiz 及其答案，希望能更深入地了解 CSS 是如何工作的。

## 问题:

```
<div class="first">
    <div class="red"></div>
    <div class="green"></div>
</div>
<div class="second">
    <div class="blue"></div>
    <div class="black"></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.red, .green, .blue, .black {
    position: absolute;
    width: 200px;
    height: 200px;
}
.red {
    background: red;
    top: 0;
    left: 0;
}
.green {
    background: green;
    top: 50px;
    left: 50px;
}
.blue {
    background: blue;
    top: 100px;
    left: 100px;
}
.black {
    background: black;
    top: 150px;
    left: 150px;
} 
```

Enter fullscreen mode Exit fullscreen mode

前面的代码片段中，四个正方形相互叠放，每个正方形都从前一个正方形的位置稍微向左移动，这样您就可以很容易地分辨出哪个在哪个的上面。

[![aligned squares](../Images/6b3fea538f776407d71382a26de21837.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RKWlrkhw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hk1gagb60wgmpn4jq1pb.png)

<center><small>squares positioned on top of each other</small></center>

如果我使用下面的代码片段来重新排列正方形的呈现顺序，那么正方形从后到前的最终顺序是什么？

```
.red { z-index: 100; }
.blue { z-index: 50; }
.green { z-index: 2; }
.black { z-index: 0; }
.first { transform: translateZ(10px); } 
```

Enter fullscreen mode Exit fullscreen mode

## 选项:

*   红色、绿色、蓝色、黑色
*   黑色、蓝色、绿色、红色
*   绿色、红色、黑色、蓝色
*   黑色、绿色、蓝色、红色

## 答案:

你可以在下面找到答案，但请给自己几分钟时间思考一下，以确保你从这个测验中受益。

* * *

* * *

* * *

* * *

* * *

## T2】👇👇👇

* * *

* * *

* * *

* * *

正确答案是:**绿色、红色、黑色、蓝色**
如果你想知道为什么这是正确答案，请阅读下面的讨论，我将很快公布答案..