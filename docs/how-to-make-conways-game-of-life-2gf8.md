# 如何制作康威的《人生游戏》

> 原文：<https://dev.to/alinp25/how-to-make-conways-game-of-life-2gf8>

生命的**游戏**，是英国数学家约翰·何顿·康威在 1970 年发明的*细胞自动化*。

所谓的*游戏*没有玩家，它的进化由它的初始状态决定，不再需要输入。我们的目标是了解一个初始的基于网格的配置是如何根据内部预先实现的一些条件而发展的。

即使一开始看起来很难，但这是实现最快、最容易理解的可视化方法之一。

[![Visual](../Images/c89ed01a12bd4d4b698070c5e64cd122.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RAcUUeRg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/alinp25/alinp25.github.io/raw/master/assets/images/gameoflife/gli.gif)

### 我们是如何做到的？

我们只需要 C++，SFML 库(当然是为了图形，谁愿意在终端里看游戏？)和一点点耐心。

我们从细胞的二维正交网格阵列(将代表我们的生命形式)开始，在它们自己的类中实现，使用不太多的方法(它们所要做的就是能够将自己画到窗口，死亡，出生，并在游戏的初始设置部分改变状态)。

电池的组件:

*   **isAlive()** >返回细胞是否存活
*   **getX()，getY()** >获取单元格的坐标
*   **draw()** >在屏幕上绘制单元格
*   **setPosition()** >在屏幕上设置位置
*   **kill()，born()** >改变细胞的状态为存活或不存活
*   **update()** >从窗口获取单元格的输入(点击时改变状态)

[![Cell setup](../Images/2fbf48472ef71afabd6e2b76aaf3e54b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---ekU9_By--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/alinp25/alinp25.github.io/raw/master/assets/images/gameoflife/cell.png)

在我们实现了 cell 类之后，游戏的其余部分比你想象的要简单。在游戏循环中，我们要做的只是遵循 3 个步骤:

1.  在 gridCopy 中克隆网格。
2.  遍历网格并基于当前条件更新 gridCopy(我们必须在 gridCopy 中更新它们，因为一旦我们在主网格中删除/创建一个新单元，下一个单元的邻居数量将小于实际数量)a)任何具有少于两个活邻居的活单元死亡，就像是由于人口不足。b)任何具有两个或三个活邻居的活细胞都可以存活到下一代。c)任何具有三个以上活邻居的活细胞死亡，好像是由于人口过多。d)任何死细胞，只要有三个活的邻居，就会变成活细胞，就像通过繁殖一样。
3.  将 gridCopy 移动到主网格中。

[![Game Logic](../Images/f33b2a1b0fe520ab2f7015e736a15da0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--waJ8niHg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/alinp25/alinp25.github.io/raw/master/assets/images/gameoflife/gamelogic.png)

[![Example Patterns](../Images/6b6acdd35c4c5344dcaa30ef2362e332.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DqibrrAG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/alinp25/alinp25.github.io/raw/master/assets/images/gameoflife/exmpl.png)

你可以在 GitHub 上看到完整的代码[，在 YouTube](https://github.com/alinp25/gameoflife) 上看到应用[的实用演示。祝你编码愉快，下次再见，*保持微笑，人生苦短，不能有 bug*！](https://www.youtube.com/watch?v=408e5MfifYQ)

###### 你可以在[我的博客](https://alinp25.github.io/gameoflifecpp/)上找到原文。