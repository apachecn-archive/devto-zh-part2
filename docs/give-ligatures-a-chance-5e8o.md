# 给连字一个机会

> 原文：<https://dev.to/slavius/give-ligatures-a-chance-5e8o>

## 连字到底是什么？

你是否发现自己经常通过改变配色方案、字体大小、行大小或制表符大小来改变和调整你的开发编辑器，以改善你的编码体验？你是否发现自己盯着一段代码看了太久，才意识到你在看一个简单的 lambda 函数和一段数学运算？

好吧，那么，你可能缺乏连字，你甚至不知道它！连字是一种字体功能，允许您将多个连续字符显示为一个自定义字形！如果你用过微软的 Word，它的替换规则非常接近连字；不同的是，这个单词实际上用替换物替换了文本，因此原始文本消失了。相比之下，只有在以下情况下，连字看起来才像一个字符:

*   你用连字字体
*   您使用支持连字的编辑器
*   您已启用连字

如果你没有其中的一个，你的代码看起来会像平常一样；没有任何改进。

## 它能为我做什么？

连字通常通过区分特殊的字符组合来提高可读性，并且通过显示一个较短的字形而不是两个或有时三个常规宽度的字形来减少代码的宽度，从而使代码更短。

我们来看一个例子:
[![font ligatures picture](../Images/214e29a2efef7f49216c000aa3ddf513.png "Font ligatures in action")](https://res.cloudinary.com/practicaldev/image/fetch/s--uzH3ViYK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/tonsky/FiraCode/master/showcases/all_ligatures.png)

有趣的是，在编辑器中，连字充当单个字形，因此您可以将其作为单个字符进行复制和粘贴。但是，在基础文件中，它会根据连字代码将空格作为多个字符。

那么让我们来看看一些带连字的样例代码:
[![ligatures in editor](../Images/7fe97690d36bac6fd759e5047a49b292.png "Font ligatures in VS Code")](https://res.cloudinary.com/practicaldev/image/fetch/s--pDBBhP9e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://oi67.tinypic.com/11ceiox.jpg)

请特别注意:

*   for 循环->
*   ==比较
*   html 标签

易于识别的逻辑操作符和标记格式增强了视觉效果。

## 哪里可以拿到？

有多种字体支持连字。

我最喜欢的免费的是 [Fira 码](https://github.com/tonsky/FiraCode)。

还有其他选择，如:

*   [用途](https://github.com/i-tu/Hasklig)(免费)
*   [幺半群](http://larsenwork.com/monoid/)(自由)
*   [固定翼增压器](https://github.com/kika/fixedsys)(自由)
*   [Iosevka](https://be5invis.github.io/Iosevka/) (免费)
*   [让 aVu 没有代码](https://github.com/SSNikolaevich/DejaVuSansCode)(免费)
*   [PragmataPro](http://www.fsd.it/fonts/pragmatapro.htm) (已付费)

但是嘿，你提到了编辑支持！你说得对，结扎并不是在任何地方都有效！这里有一些概述。
工作地点:

*   VS 代码(包括 VS 探索)
*   微软 Visual Studio 2015+(包括社区版)-目前 WPF 的一个 bug 阻止了带减号/破折号(像->)的连字被转换
*   JetBrains 编辑器(CLion，Intellij，PHPStorm，WebStorm，PyCharm，Rider，...)
*   Notepad++(需要变通方法)
*   崇高的文本
*   Xamarin 工作室
*   XCode
*   gEdit
*   基于 Atom 的编辑器(1.1 以上)
*   安卓工作室

为了获得完整的支持，请看一下[工作和非工作编辑](https://github.com/tonsky/FiraCode#editor-support)和[以及终端](https://github.com/tonsky/FiraCode#terminal-support)的列表！