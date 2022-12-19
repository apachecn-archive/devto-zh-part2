# 名单去喉:摘要

> 原文：<https://dev.to/robinheghan/de-throning-the-list-summary-3f3c>

我一共写了五篇(或者六篇，包括这篇)关于为什么我想用`Array`代替`List`的文章。如果你错过了一个，它们都在这里:

*   [简介-宏伟计划](https://dev.to/skinney/de-throning-the-list-2fjk)
*   [Part Deux-Improving`Array`](https://dev.to/skinney/de-throning-the-list-part-deux-4idm)
*   [部分π -可停止折叠操作](https://dev.to/skinney/de-throning-the-list-part--44dl)
*   [部分 SC4K - API 变更](https://dev.to/skinney/de-throning-the-list-part-sc4k-4e3n)
*   [零件硼-文字表示法](https://dev.to/skinney/de-throning-the-list-part-boron-185)

这些帖子列出了大量的工作。首先，我们需要修改`Array`实现，以在更多情况下提高性能。然后我们需要通过引入可停止的折叠操作使它更具可扩展性。然后我们需要为`List`添加现有的功能，但不为`Array`添加。最后，我们可以让 Elm 编译器直接在 javascript 代码中输出`Array`的文字表示。

但是为什么所有这些都有效呢？因为我真诚地相信，如果默认集合类型不仅与其他语言中的默认集合类型工作类似，而且在您希望使用它的大多数情况下提供了不错的性能，Elm 会变得更容易学习。我也相信 Elm 在一般情况下会变得更快，如果今天所有使用`List`的地方都使用`Array`。

我可能不对。但我确实认为这是一项值得承担的任务。或者...哦...你知道我的意思。

这个旅程的下一步也是第一步是将`Array`实现从 HAMT 升级到 RRB 树。这个过程可能需要一段时间，但是当它发生的时候，期待一篇有基准测试结果的新博文。

感谢您的阅读！