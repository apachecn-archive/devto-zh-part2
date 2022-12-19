# gamoliyas——JavaScript 中的生活游戏(2007 年 8 月)

> 原文：<https://dev.to/jalbam/gamoliyas--game-of-life-in-javascript-august-2007-48ej>

“Gamoliyas”是一个完全用 DHTML (HTML，CSS 和 JavaScript)编写的开源康威生活游戏版本(高度可配置)。

[![Screenshot](../Images/4af0b78906e2275ab5c0e14340673953.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W7_ChwA6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://dhtmlgames.com/gamoliyas/gamoliyas.gif)

这是我的第九个 DHTML 游戏，制作于 2007 年中期。

玩家可以配置游戏的许多选项，如改变世界大小，在球形和有限世界之间选择，使用根据邻居数量变化的颜色，改变速度，保存一个世界或打开一个先前存储的世界，自己绘制细胞或随机生成细胞，等等。

这个游戏被特别设计成允许在任何其他网站中使用(例如，通过使用 IFRAME)。我们只需要获得由“保存世界”选项生成的 URL(将在 IFRAME 标签的 SRC 参数中使用),并具有我们在游戏将询问我们的选项中决定的所需行为(世界大小、自动游戏、球形世界、多色、隐藏顶部菜单、隐藏底部控件、隐藏底部信息面板、不允许绘制、速度、单元格大小和单元格之间的空间)。通过该 URL 访问的游戏将开始包含当我们按下“拯救世界”选项时出现的单元格。你可以在我的个人投资组合中看到一个使用 IFRAME 的例子:[http://www.joanalbamaldonado.com/portfolio/](http://www.joanalbamaldonado.com/portfolio/)

你也可以通过编辑脚本开头的配置变量来改变游戏行为。要改变游戏图像，你只需要用其他图像替换它们。

这个游戏只使用了一些图片，如果我们在游戏中使用面板和这样的隐藏，这些图片是不必要的。所以你能看到的所有其他东西都是用纯 HTML 和 CSS 完成的。

官方语言是西班牙语和英语。

你可以在互联网上找到这个游戏的其他修改和改编版本，包括一个 Opera widget。

这款游戏已经在 BeOS，Linux，NetBSD，OpenBSD，FreeBSD，Windows，Mac OS X，BlackBerry Tablet OS，Android，iOS 等平台下进行了测试。

在线英语游戏:[http://gamoliyas.tuxfamily.org/gamoliyas_english/](http://gamoliyas.tuxfamily.org/gamoliyas_english/)

英语在线游戏(镜像):[http://www.dhtmlgames.com/gamoliyas/gamoliyas_english/](http://www.dhtmlgames.com/gamoliyas/gamoliyas_english/)

西班牙语在线游戏:[http://gamoliyas.tuxfamily.org/gamoliyas_spanish/](http://gamoliyas.tuxfamily.org/gamoliyas_spanish/)

西班牙语在线游戏(镜像):[http://www.dhtmlgames.com/gamoliyas/gamoliyas_spanish/](http://www.dhtmlgames.com/gamoliyas/gamoliyas_spanish/)

官方网站:[http://gamoliyas.tuxfamily.org/](http://gamoliyas.tuxfamily.org/)(镜像在[http://www.dhtmlgames.com/gamoliyas/](http://www.dhtmlgames.com/gamoliyas/))。

也可以在 GitHub 上找到:[https://github.com/jalbam/gamoliyas](https://github.com/jalbam/gamoliyas)