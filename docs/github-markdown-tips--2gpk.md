# GitHub 降价提示

> 原文：<https://dev.to/burdettelamar/github-markdown-tips--2gpk>

我早些时候发布了 Richard Kim 的博客文章，其中有一些*关于在 GitHub 上构建有效 README.md 页面的很棒的*技巧。

我自己的一些想法:

*   不只是 README.md:

    *   你可以有任意数量的降价页面。
    *   它们可以在任何目录中。
    *   它们可以有除 README.md 之外的文件名。
    *   唯一的“要求”是 GH 要正确呈现一个 markdown 页面，它必须有`.md`文件扩展名。
*   你可以用一个普通的 GitHub URL 链接到一个*页面*:

    *   ht *tps://github.com/ *用户* / *项目* /blob/ *分支* / *文件* .md*
    **   这使得读者可以在页面顶部看到各种项目信息。(实际的降价文本将在页面的更下方。)*
**   您可以链接到带有扩展 URL 的页面上的降价标题中的标题*:*

    *   ht *tps://github.com/ *用户* / *项目* /blob/ *分支* / *文件*。md# *标题*。*
    **   这使得浏览器(至少是 Firefox)恰好位于标题处，而不是页面顶部。(从标题的链接图标中获取准确的 URL。)*   目标标题可以是页面上的任何标题(不仅仅是主标题，用一个`#`标识)。)***   这对于在降价页面之间链接*特别有用，这样读者就可以进入降价文本，而不是页面顶部。*

    *   我已经用我项目的[测试旅程](https://github.com/BurdetteLamar/RubyTest/blob/master/examples/github/TesterTour.md#tester-tour)做到了这一点，旅程中的每一页都有向前和向后的导航链接。

    *   我有一个`MarkdownHelper`类，用来:

    *   通过添加导航链接来构建旅程。
    *   通过内联包含外部文件。
    *   如果文件是`.rb`或`.xml`，则添加文本高亮显示。(这可以扩展到[GitHub](https://github.com/github/linguist/blob/master/lib/linguist/languages.yml)已知的任何语言)。
    *   质疑:这个类应该做成红宝石吗？*   从 markdown 链接到 HTML 文件:

    *   如果按照通常的方式链接，则不会呈现 HTML:ht*TPS://github . com/*用户* / *项目* /blob/ *分支* / *文件*。超文本标记语言*
    **   如果用特殊方式链接，HTML *就会*渲染:ht*TPS://HTML preview . github . io/？ht *tps://github.com/ *用户* / *项目* / *文件*。超文本标记语言*****   我会举例说明，但是从这里的*开始*打开任何一种链接都不会导致 HTML 被渲染。只有在 GitHub markdown 中才能看到这种差异。*   但是你可以通过[https://github . com/BurdetteLamar/ruby test/blob/master/examples/changes _ report/changes report . MD](https://github.com/BurdetteLamar/RubyTest/blob/master/examples/changes_report/ChangesReport.md)下面的链接看到它是如何工作的。*****