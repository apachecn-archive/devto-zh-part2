# 实体游戏桌如何激发我的下一个项目

> 原文：<https://dev.to/darksmile92/how-a-physical-game-table-inspired-my-next-project-6p7>

前几天晚上，我们去了一个叫做“Pyramide”的地方，这是一个酒吧/酒馆，结合了一个游戏娱乐区。

这里有台球桌、拱廊机，你可以玩飞镖，还有像这样的精彩赌桌:

[![Gaming table google result](../Images/71967f4cd6ae180e3123ff68ad477ef9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yEjFx1I1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zdnufkf23ym57chjz1gv.jpg)

我们玩大富翁和其他游戏玩得很开心，但有一个游戏对我来说是新的: **Filler**

规则很简单:

*   拥有至少 50%的石头才能获胜
*   你只能使用你之前的人没有使用过的颜色，而不是你实际使用的颜色
*   你只能拥有与你颜色相同的新宝石，如果它们还没有被拥有的话
*   你只能拥有新的石头，如果它们和你已经拥有的连接在一起的话

[![Filler android game screenshot](../Images/3f98a265e32bb9105b87766d32b172d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DsFi5GZM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tz8hygj6ocf9fu3mqlar.png) 
*来源:我发现的[安卓 app](https://play.google.com/store/apps/details?id=org.xbasoft.fillerclassic&hl=en_US) 的 Play store 截图*

## 项目

因为目前我用 react 做了很多，所以我选择 React 来构建游戏。
[试玩](https://darksmile92.github.io/filler-game-react/)

#### 范围

*   将基本游戏构建为 react 应用程序
*   增强与对手的游戏
*   让它变聪明

#### 当前状态

现在游戏在单人模式下运行，而我正在积极地运行对手模式。

#### 待办事宜/计划

*   为对手实施人工智能/策略
*   创造不同的解决策略
*   重构/清理代码

#### 为什么我要告诉你这些？

我很想得到你的一些意见，我认为这是一个很好的基础，可以开始一个有潜力的小游戏项目。这也是我第一次在 react 中做这样的事情，我以前只用 C#、Python 和 C++创作过游戏。

也许我们可以一起提出一个挑战(就像 Robocode ),这样当项目更加成熟时，每个人都可以编写一个求解器策略？

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png) [暗黑微笑 92 ](https://github.com/DarkSmile92) / [填充-游戏-反应](https://github.com/DarkSmile92/filler-game-react)

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