# 在你的网站上添加表情符号图标！

> 原文：<https://dev.to/pickleat/add-an-emoji-favicon-to-your-site-co2>

嘿，这是我在 dev.to 上的第一篇帖子，所以我想分享一下我学到的让网站看起来更好的小技巧！

首先，如果你不知道什么是 favicon，不要担心！我直到最近才知道。它是你选项卡上的小缩略图。如果你使用谷歌浏览器，它看起来是这样的:

[![Favicons!](img/73e4cb69419125b4791fb29e41c17d11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tU_3VSN2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/pickleat/dev.to/master/Screen%2520Shot%25202018-11-08%2520at%25201.22.49%2520PM.png)

我一直在为网络编程课程 CS50 做一个项目 1。任务:用 Flask 和 PostgreSQL 建立一个书评网站。当我在做的时候，我有点恼火我的网站看起来不是很专业。为了便于格式化，我使用了 Bootstrap，但是它看起来并不好，只比默认的好一点点。我决定添加一个 favicon 会有所帮助，但我不想弄乱制作一个标志或找到一张图片并缩放它，所以我想。也许表情符号会有用？的确如此，太不可思议了！

我觉得找到我想要的表情符号并把它添加到我的

of my template.html. Here's how the code ended up looking:

[![Code Screenshot](img/5712873a658dfa2a89d006df3a36ee71.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QAh1d_qQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/pickleat/dev.to/master/Screen%2520Shot%25202018-11-08%2520at%25201.34.58%2520PM.png)

这增加了一个伟大的触摸，如果一个用户像我一样，一直有 15 个标签打开，网站图标，会帮助他们找到正确的页面。

我是这样做的:

1.  前往[https://emojipedia.org/](https://emojipedia.org/)
2.  找到你的表情符号。
3.  右键点击你喜欢的表情符号风格(我是苹果人)
4.  复制链接并插入到您的`<head>`标签中，如下所示:`<link rel="icon" href="https://emojipedia-us.s3.dualstack.us-west-1.amazonaws.com/thumbs/240/apple/155/books_1f4da.png">`

你完了！我在这个项目中使用 flask，所以我把它放在布局页面中，它动态地放在任何顶部有

`{% extends "layout.html" %}`

的模板中。html 文档。

这是它的样子！

[![Look at the icon in the tab!](img/b7aa0c50dd59b04735d3de2d4e126ac9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m0BTaTyq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/pickleat/dev.to/master/Screen%2520Shot%25202018-11-08%2520at%25201.36.46%2520PM.png)

正如你所看到的，我仍然在本地主机上测试，还没有完全完成这个项目，但它会在我的[个人资料网站](https://pickleat.github.io/)上，以及它自己的[回购](https://github.com/pickleat/BookReviewSite)上。

感谢阅读！如果你喜欢这个，让我知道。如果有更好的方法，请告诉我！欢迎随时关注我的 [Twitter](https://www.twitter.com/pickleat) 或 [Github](https://github.com/pickleat) ！