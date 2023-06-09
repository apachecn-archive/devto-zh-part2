# 俄罗斯方块 Java 版俄罗斯方块(2008 年 5 月)

> 原文：<https://dev.to/jalbam/tetrisdai--tetris-in-java-may-2008-2kf> 爪哇 https://dev.to/jalbam/tetrisdai 俄罗斯方块

“TetrisDAI”是用 NetBeans 用 Java 1.6(又名 Java SE 6)编写的开源俄罗斯方块克隆(高度模块化)。

[![Screenshot](img/88d54c74099fcce39ef8de4e5e6536f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5_c-UQ4B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.dhtmlgames.com/tetrisdai/tetrisdai.jpg)

这是我的第一个 Java 游戏，制作于 2008 年中期。

代码非常模块化，任何人都可以很容易地修改和配置。例如，您可以更改控制键、它们的延迟、添加不同形状的新棋子或更改当前的棋子甚至删除它们、更改整个游戏(包括棋子)的颜色、更改游戏视觉外观和大小(单元格和地图的宽度和高度、文本外观、边距)、决定是否使用图像或只是简单的计算机生成的图形作为单元格和背景、决定语言(游戏已经翻译成西班牙语， 加泰罗尼亚语和英语，并且只需要改变一个变量以在这些语言中选择一种)或者容易地添加新的语言，改变菜单项，改变初始速度和到达下一级时的速度增加，改变完成一级所需的行，修改分数系统(放置的棋子的分数，每行的分数和多行的指数分数增加)，启用或禁用声音等等。

官方语言是英语，但代码也包括西班牙语和加泰罗尼亚语的翻译(如前所述，只需改变一个变量，所有的游戏将使用所需的语言)。

这款游戏已经在 Linux，NetBSD，OpenBSD，FreeBSD，Windows，Mac OS X，Haiku OS 等平台下测试过。

官方网站:[http://tetrisdai.tuxfamily.org/](http://tetrisdai.tuxfamily.org/)(镜像在[http://www.dhtmlgames.com/tetrisdai/](http://www.dhtmlgames.com/tetrisdai/))。

也可以在 GitHub 上找到:[https://github.com/jalbam/tetrisdai](https://github.com/jalbam/tetrisdai)