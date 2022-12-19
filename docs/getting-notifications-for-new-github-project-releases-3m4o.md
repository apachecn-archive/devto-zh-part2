# 获取新 GitHub 项目发布的通知

> 原文：<https://dev.to/raymondcamden/getting-notifications-for-new-github-project-releases-3m4o>

这是又一篇关于我认为大多数人已经知道的东西的文章。我自己也是几周前才发现的。这绝对不是什么新鲜事，事实上，你可以从两年前的中找到 StackOverflow 的答案。但正如我通常所做的，我分享我所学到的，因为我认为我不可能是唯一一个错过这个的人。

所以正如标题所言——如何在 GitHub 上获得项目新发布的通知？原来每个回购都有一个 Atom feed:

[https://github.com/(org](https://github.com/(org) 或 user)/(repo)/releases.atom

例如，这是我最喜欢的新项目之一的提要， [VuePress](https://vuepress.vuejs.org/) :

[https://github . com/vuej/vuepress/release . atom](https://github.com/vuejs/vuepress/releases.atom)

就这样了。当然,“通知”方面包括读取 Atom 提要，并判断何时添加了新内容。幸运的是， [IFTTT](https://ifttt.com/discover) 有这方面的服务。如果你从未使用过它，IFTTT 本质上是一个网络“自动化”服务，有数百个插件/连接/等等，用于各种服务。他们有一个最简单的解决方案，那就是“用电子邮件通知我 RSS 提要中的新内容”服务。这对于没有订阅功能的博客来说非常有效，在这里也同样有效。

简单地[创建一个新的 applet](https://ifttt.com/create) ，第一步，选择 RSS Feed:

[![I like arrows](../Images/e5a6fd6ec45e7705b34967fe66c44a5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uqj2ASto--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.com/images/2018/05/ifttt1.jpg)

然后选择“新订阅源项目”:

[![I love arrows](../Images/ea83ae43affa00fba549a8971f5d780e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HuFF69LG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.com/images/2018/05/ifttt2.jpg)

然后粘贴到源 URL:

[![Sorry, no arrows](../Images/9d41c579a2c3881204137cb72c2f6a0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lFWwH_td--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.com/images/2018/05/ifttt3.jpg)

单击“Create trigger ”,然后进入“that”方面(即，当一个新项目出现在提要中时应该发生什么)。在“操作服务”页面上，选择电子邮件:

[![Woot the arrows are back!](../Images/e3c21a238909966dc187a4fdb501d832.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X8tE4DF4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.com/images/2018/05/ifttt4.jpg)

这只给你一个选项(所以我跳过截图)，“给我发邮件”。您现在可以自定义电子邮件。我建议在主语前加一个前缀，让事情更明显:

[![Sigh, no arrows](../Images/b40cc0e5df80ce7536defe2a7223b618.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3Uf6Dq-e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.com/images/2018/05/ifttt5.jpg)

如果你愿意，你也可以在那里定制更多。最后点击“创建操作”。您将进入审查页面，并希望确保点击“完成”按钮。

仅此而已。希望这有帮助！