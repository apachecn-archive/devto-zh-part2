# Kamisado，一个抽象棋盘游戏的 JavaScript 实现。

> 原文：<https://dev.to/_bigblind/kamisado-a-javascript-implementation-of-an-abstract-board-game-1d0p>

我有一个问题。我的`~/dev`文件夹里有**道**太多未完成的附带项目。所以我想快速构建一些东西，并承诺尽快发布和展示它。这就是了！

为了获得最佳体验，[点击这里进入 CodePen 的全屏视图](https://codepen.io/bigblind/full/xWBrxP/)。但是为了方便起见，我把它嵌入在这里。

[https://codepen.io/bigblind/full/xWBrxP/%20?height=500&default-tab=result&embed-version=2](https://codepen.io/bigblind/full/xWBrxP/%20?height=500&default-tab=result&embed-version=2)

我用 p5.js 构建了它，主要是因为我不必处理原始的 canvas API。

# 关于神道

Kamisado 是一个抽象的棋盘游戏，由两个玩家在一个格子上玩，格子上有不同颜色的方块。每个玩家有 8 个棋子，每个棋子对应一种颜色。

第一个玩家，白棋，可以选择移动哪一个棋子。第一次移动后，您必须始终移动与最后一个玩家移动到的方块颜色相对应的棋子。你可以将一个棋子向前或斜向前移动你想要的距离。不要侧身或向后。

将你的一枚棋子移到对方的本排，你就赢了。

# 已知缺点

这个版本只包括神道的短模式。在真正的游戏中，一旦你到达另一边，你可以继续玩，通过将你的棋子变成相扑，可以推动其他棋子。我可能会在这个版本的基础上增加这个功能。

代码不是很漂亮。我使用蛇形大写字母表示全局变量，而不是常量，因为它们通常在 JavaScript 中使用。我也写过纯过程化的代码(没有类)，我不认为结构有它应有的好。

# 路标

我想在这里写一篇关于棋盘游戏人工智能的文章，并想以神道为例实现一个简单的人工智能。下周我会在这方面努力。

我也希望允许人们通过互联网玩游戏。我正在尝试决定是使用 firebase 还是 WebRTC 数据通道来实现这一点。