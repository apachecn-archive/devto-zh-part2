# [更新 v1]罗宾的填充游戏

> 原文：<https://dev.to/darksmile92/update-v1-filler-game-by-robin-4do7>

本周早些时候，我发了一篇帖子，讲述了我是如何受到启发用 react 制作这个游戏的

[![darksmile92 image](img/722f3759d16dd71a85fce5693df871b5.png)](/darksmile92) [## 实体游戏桌如何激发我的下一个项目

### robin Kretzschmar 11 月 12 日 182 分钟阅读

#react #game #inspiration #algorhythm](/darksmile92/how-a-physical-game-table-inspired-my-next-project-6p7)

今天，我检查了一个更新。
新功能现在包括:

*   2 人模式(逐回合切换)
*   新游戏按钮
*   显示统计数据(50%中有多少%获胜)
*   新徽标/网站图标
*   选项:在单元格上显示所有者
*   选项:在按钮建议中显示通过颜色获取的单元格数量

下面是 GitHub 上托管的[Demo](https://darksmile92.github.io/filler-game-react/):
[![Screenshot of demo](img/74be1445cd16b52b3bfeb25dff63da3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mqu3XCnf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xlthgcsm5avap7hr3vb8.png)

#### 接下来

*   实现 CPU 对手
*   实施不同的解决策略

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [暗黑微笑 92 ](https://github.com/DarkSmile92) / [填充-游戏-反应](https://github.com/DarkSmile92/filler-game-react)

### 作为 react app 实现的经典填充游戏！

<article class="markdown-body entry-content container-lg" itemprop="text">

## 作为 react 应用的填充游戏！

### 开始

在您选择的终端中运行以下命令:

```
git clone https://github.com/DarkSmile92/filler-game-react.git filler-game-react
cd filler-game-react
yarn
yarn start 
```

### 演示

参观[https://darksmile92.github.io/filler-game-react/](https://darksmile92.github.io/filler-game-react/)观看演示。

### 扭

#### 建议

现在，游戏只需计算每种颜色可能获得的最大细胞数，并使用最大值作为建议，就可以找到最佳的下一步。具有该颜色的按钮将带有边框。如果有多个相同的可能性，则只考虑第一个。

### 求解算法

目前还没有自动采取步骤的算法。接受挑战，在`./src/solver/`路径下创建解决方案并分享它们！

### 使用 Visual Studio 代码调试

首先将扩展 [Chrome 调试器](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)安装到 VSCode 实例中。

在终端中运行`yarn start`，在 VSCode 中设置断点，然后按`F5`开始调试。

</article>

[View on GitHub](https://github.com/DarkSmile92/filler-game-react)