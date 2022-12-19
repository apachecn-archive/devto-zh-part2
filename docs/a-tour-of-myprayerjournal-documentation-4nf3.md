# myPrayerJournal 之旅:文档

> 原文：<https://dev.to/danieljsummers/a-tour-of-myprayerjournal-documentation-4nf3>

*备注:*

*   这是系列文章中的第 7 篇；请参见[介绍](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-introduction-jl5)中的所有内容，以及构建该软件的要求。
*   *以文本“mpj:”开头的链接是指向 myPrayerJournal 的 1.0.0 标签(1.0 版本)的链接，除非另有说明。*

我们已经花了很多时间从软件开发人员的角度看这个应用程序的各个方面。然而，还有另一个角度需要考虑——用户的角度。对于一个“极简主义”的应用程序，我们如何让他们知道按钮是做什么的？

## 设置 GitHub 页面

在其他项目中，我们通过创建库的`gh-pages`分支来使用 [GitHub 页面](https://pages.github.com)。这有一些优点，比如页面结构完全独立于源文件。但是，与此同时，您要么必须在不同的分支上有两个沙箱，要么在从编码切换到文档时切换分支。幸运的是，这不是唯一的选择；GitHub 页面也可以从`master`分支的`/docs`文件夹中发布。他们有一个关于如何设置的很好的指南。

当我们从 repo 的设置页面中选择主题时，GitHub 将同样位于`/docs`文件夹( [mpj:docs 文件夹](https://github.com/bit-badger/myPrayerJournal/tree/1.0.0/docs))中的`_config.yml`文件放在那里。我们放在那里的唯一文件是`index.md` ( [mpj:docs/index.md](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/docs/index.md) )。

## 撰写文档

由于 GitHub Pages 在幕后使用了 [Jekyll](https://jekyllrb.com) ，我们可以使用 [Markdown](https://daringfireball.net/projects/markdown/) 和默认的 [Liquid](https://github.com/Shopify/liquid/wiki) 标签。不过，我们不需要任何液体标签，因为文档非常简单。你可能会注意到在`index.md`的顶部没有任何前面的东西；如果没有，GitHub Pages 会从文档的第一个`h1`标签中选择页面的标题，用前导的`#`序列定义。

如果您浏览`index.md` 的[提交历史，您会看到许多提交要么引用了版本号，要么引用了发行号。这使得追溯源代码变得非常简单，不仅可以查看代码，还可以查看文档中是如何解释其功能的。你也可以回顾已经犯下的错别字，这有助于保持你的谦逊。:)](https://github.com/bit-badger/myPrayerJournal/commits/1.0.0/docs/index.md)

## 变得更好

还可以做更多的工作来改进项目的这一方面。第一种方法是给它分配一个`prayerjournal.me`的子域，而不是提供来自`bit-badger.github.io`的页面。GitHub Pages 支持自定义子域，甚至通过[加密](https://letsencrypt.org)来支持 HTTPS。第二是为页面设计一个轻量级的主题，与主站点的外观和感觉相匹配；这将带来更加统一的体验。最后，虽然“极简主义”是目标，但最终还是有一些需要大量文字来解释的功能；页面上的目录可以帮助人们直接找到他们需要的帮助。

我们的旅行即将结束；下一次，我们将总结对开发过程的观察和意见。