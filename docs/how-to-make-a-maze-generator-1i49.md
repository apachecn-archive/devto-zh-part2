# 如何制作迷宫生成器

> 原文：<https://dev.to/alinp25/how-to-make-a-maze-generator-1i49>

[![Screenshot](../Images/b5f492e995f5bd71c7c2d4a7d3aae2d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ki3GNZ9l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/alinp25/alinp25.github.io/master/assets/images/mazegeneratortutorial/screenshot.png)

我们要做什么？迷宫发电机。怎么会？让我们看看。

* * *

### 我们需要什么？

我们将用 C++制作这个项目，使用 [SDL 库](https://www.libsdl.org/)制作图形。他们有一个非常好的设置 SDL 的教程，非常简单快速，[就在这里](https://wiki.libsdl.org/Installation)。

**我们来编码吧！**

首先，我们对迷宫的理解是什么？嗯...让我们来看看字典:*一个错综复杂的路径或段落网络*。我不喜欢它听起来的样子，让我们把它说得更简单些:有或没有连接的房间的集合。那么，我们该如何构建它呢？在正常情况下，它可以被构造成一个图形，但为了简化这个问题，我们将它作为一个 NxN 网格。

现在有趣的部分来了，思考开始了...网格中的每个单元格应该包含什么？姑且称之为房间吧。正常的房间是什么样的？墙壁。所以，理论上，我们房间之间的联系，是由墙来代表的。 [![Room from maze](../Images/7ac930077c67f376e5e1784a3604a868.png)](/assets/images/mazegeneratortutorial/roommaze.png) 。

现在，我们如何连接两个房间？通过拆除它们之间的墙。我们如何做到这一点？通过拆除两面墙。现在问题来了“等等，在两个房间之间我们有两面墙”。理论上没有，但在这个模型中，是的，因为当你移除房间的左墙时，你必须移除房间左边的右墙。有点令人困惑，但这张图片可能会让它更清晰。

[![Wall Diagram](../Images/5017319f0e94d89b3ed35eddecdaac48.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iOtcQIQg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/alinp25/alinp25.github.io/raw/master/assets/images/mazegeneratortutorial/roommaze.png)

### 算法长什么样？

该算法基于回溯解决方案(你也可以找到 Kruskal 和 Prim 关于随机迷宫的版本),它是这样工作的:

1.  选择一个房间，将其作为起点，并标记为已访问
2.  当有未参观的房间时
    1.  如果当前小区有没有被访问过的邻居
        1.  随机选择一个还没有去过的邻居
        2.  将当前房间推到堆栈中
        3.  移除当前房间和下一个堆栈之间的墙(再见墙，欢迎道路)
        4.  使当前单元格*成为选中的单元格*，并将其标记为已访问
    2.  否则，如果没有未访问的邻居并且堆栈不为空
        1.  从堆栈中弹出一个单元格
        2.  使其成为当前单元格

[![Algorithm](../Images/5d40a7d8c56eb7939e2a6649068f58e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CphCs7xP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/alinp25/alinp25.github.io/raw/master/assets/images/mazegeneratortutorial/walldiagram.png)

这是一个非常简单的算法，可以产生一些非常有趣的视觉效果，在某些情况下很有帮助...由此，我童年时代认为书上的迷宫是手工设计的想法被打破了。你可以在这里查看[，在这里](https://github.com/alinp25/MazeGenerator)查看[。](https://www.youtube.com/watch?v=qKDWKDohnBw)

###### 你可以在[我的博客](https://alinp25.github.io/)上找到原文。