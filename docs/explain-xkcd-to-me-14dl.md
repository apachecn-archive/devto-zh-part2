# 给我解释一下 XKCD

> 原文：<https://dev.to/yechielk/explain-xkcd-to-me-14dl>

#### 制作我的第一个浏览器插件

我有一种感觉，我的大多数粉丝已经知道了 XKCD，这个网络漫画已经成为了书呆子文化的主要内容，如果你还不知道，那么恭喜你！你是今天的[幸运万人之一！](https://www.xkcd.com/1053/)

[![XKCD's "Ten Thousand" webcomic](img/a5e96bf6957cbe02f97c1c9acd39eb67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1nSKUXQt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/ten_thousand.png)

我已经虔诚地追随 XKCD 很多年了，并且非常喜欢每一部漫画。但是，偶尔也会有一个是我得不到的；也许是一个模糊的数学参考或编程参考，在那之前，成为我的事情。在那样的日子里，我感谢 [Explain XKCD](https://www.explainxkcd.com/wiki/index.php/Main_Page) ，一个类似维基的网站，书呆子们在这里聚在一起解释所有的网络漫画。

[![Explain XKCD logo](img/90d65ec31b146e9a911ab8e404398094.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZjUr1L0T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/257/0%2ApLQaAaLAOh6GKsKb.png)

几天前，我在寻找一个小项目来帮助我进入浏览器插件的世界，我想到了给每一个 XKCD 漫画添加一个链接，将用户直接带到 Explain XKCD 上的相应页面。

当我开始的时候，我不知道制作一个浏览器插件需要什么，但我想我会给它一个晚上的时间来开始，也许在周末完成它。

我最终惊讶于它是如此的简单，在坐在电脑前的两个小时内，我已经阅读了文档，编写了一个工作插件，打包了它，并在 [Mozilla 的附加商店上获得了批准！](https://addons.mozilla.org/en-US/firefox/addon/xkcd-explainer/?src=search)

<figure>[![Screenshot of the plugin in action](img/4d469f7a567fef037f33a1060a96156d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o8YFsNER--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/621/1%2AOTYUy9D2mGuf-kYjVQ_ncA.png) 

<figcaption>注意到新按钮了吗？</figcaption>

</figure>

Mozilla 有[优秀的文档](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions),从头到尾描述了整个过程，包括你可以跟着编码的示例插件。

这个过程本身相对简单。浏览器插件至少包含一个 manifest.json 文件，其中包含关于插件的信息(名称、版本、它应该工作的 URL)，以及一个包含实际代码的 JavaScript 文件。

当然，JavaScript 可以变得像您希望的那样复杂，但是在我的例子中，整个插件包含不到 20 行代码。

如果你和我一样喜欢 XKCD，如果你下载了这个扩展(你可以在这里找到它[火狐的](https://addons.mozilla.org/en-US/firefox/addon/xkcd-explainer/?src=search)和在这里找到[Chrome 的](https://chrome.google.com/webstore/detail/xkcd-explainer/iicndfighifkfkpijajnnhhbdmnohcml))并让我知道它是如何为你工作的，我会非常感激！

GitHub 项目页面总是欢迎反馈。

* * *

*这篇文章是从我的博客 [Rabbi On Rails](https://blog.yechiel.me/explain-xkcd-to-me-145e2e7db9b3) 交叉发布的。
你可以在那里了解更多关于我的编程之旅，或者在 Twitter 上关注我 [@yechielk](https://twitter.com/yechielk)*