# 我的第一个颤振应用！

> 原文：<https://dev.to/sandordargo/my-first-flutter-app-3m75>

如果你在关注我的帖子，你可能记得我最近去了里维埃拉 2018 年发展大会，在那里我参加了一个非常有趣的关于 Flutter 的演讲，演讲人是 [Florian Loitsch](https://github.com/floitschG) 。 [Flutter](https://flutter.io/) 是一个为 Android 和 iOS 设备开发原生界面的框架，然而你只保留了一个代码库。

我开发了几个 Android 应用程序，主要是为了我(和我妻子)的娱乐，以及我自己的教育。Loitsch 在他的会议期间在我们面前轻松地创建了一个新的 Flutter 应用程序，这让我感到很有趣。我想尽快尝试一下。

我只是需要一个主意。当我在家里享受第二个孩子的陪产假时，有一次我去当地的 DIY 商店，一位听起来非常严厉的女士在广播中非常愤怒地说，包括法国总统本人在内的法国人喝了太多的酒。她详细阐述了这个话题，援引总统马克龙的话说，他每顿午餐和晚餐都会喝一杯葡萄酒。想想一杯 100 毫升的酒，即使他不喝其他东西，一周也已经是 14 单位了。

根据一些新的研究，你一周不应该喝超过 10 单位的酒，你应该有两天不喝酒。抱歉，我没有找到英文版的。不说法语的人多幸运，你还有一个[推荐的 14 个单位](https://www.drinkaware.co.uk/alcohol-facts/alcoholic-drinks-units/how-much-is-too-much/) :D(旁注:[英语](https://en.wikipedia.org/wiki/Unit_of_alcohol)和[法语](https://fr.wikipedia.org/wiki/Unit%C3%A9_d%27alcool)计算单位不同。显然，14 个英制单位对应 11.2 个法语单位。还是。那是一小杯酒。)

我在想，一顿饭喝一杯酒其实并不过分。这位女士说这太多了。百米...我到底喝了多少？我应该量一下。Bamm！

于是有了这个想法。当然，我可以下载一个应用程序...但是自己造一个更有趣，不是吗？

于是我开始了。

第一步也是最重要的一步，我想保存我所喝饮料的数据。为此，SQLite 数据库似乎是完美的。

一开始，我想要简单的东西，所以我有两个屏幕。

1.  列出我喝过(存过)的所有饮料。
2.  添加新饮料。一种饮料有一个名字，一个消费日期，一个容量，一个强度，从前面两个计算的酒精单位和一个可选的备注。

在这一点上，我只使用应用程序进行测试，因为数据丢失的可能性太大了。我需要一些同步。

但之前，我添加了一些小功能，使现有的部分更舒适，我在应用程序中添加了编辑和删除功能。

[![BoozeTracker List of Drinks](../Images/ce66acaa3a0292463b98e839fed3ad74.png "BoozeTracker List of Drinks")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fgtcjHFx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/raivmfk1082gbwt4cuhn.png)

是时候回到同步部分了。首先，我必须想出一个解决方案。所以我必须明确我的需求。

我不想丢失数据，以防我必须在另一部手机上安装该应用程序。我想存储我前面描述的数据，一个 SQL 表的数据。

我的数据可以很容易地用 CSV 格式表示。为了不丢失数据，我可以简单地将数据转储到 CSV 文件中，然后通过电子邮件发送。不是最创新的解决方案，但我已经在流行的应用程序中看到过。

因此，作为第一次迭代，我将数据导出到一个 CSV 文件中，并通过电子邮件发送给自己。下一步，我希望能够导入这些数据。

我[在我的 GoogleDrive 上发布了 csv](https://support.google.com/docs/answer/183965?co=GENIE.Platform%3DDesktop&hl=en) ,我只是通过一个 HTTP GET 请求从那里读取它。一旦数据存在内存中，就只需要更新数据库了。是的，在这一点上，如果我导入，我只是简单地替换了我的本地数据的全部内容。

正如您可以感觉到的，这种解决方案可以保存数据，但它一点也不舒服，因为每次我想执行导入时，我都必须从最新的可用备份中手动更新我发布的电子表格。

使用 Google Drive 似乎是一个免费且相对舒适的解决方案，所以我开始发现它的 API。不幸的是，对于 Flutter 来说，目前还没有可用的本地 API，因此我不得不使用他们的 REST API。最后，这可能是一件好事，因为它是跨平台/跨语言的。

在这一步结束时，我停止发布 CSV 文件，并通过 Google Drive API 更新和读取该文件。

这个解决方案仍然可以改进，因为它仍然是手动调用的(用一个按钮)，并且一个仍然完全覆盖另一个。但是对于我的需求来说已经足够好了，所以我去实现其他特性。

我在屏幕上方的应用程序栏有很多按钮，有点令人不知所措，所以我实现了一个 [*抽屉*](https://flutter.io/cookbook/design/drawer/) 以便于不同活动之间的导航。

我还实现了一些小部件(在 Flutter 中一切都是小部件)来提供酒精消费的统计数据。

一个名为`MainStats`的提供了过去 X 天的总消耗量和干燥天数。它根据用户定义的一些限制对值进行着色。(绿色表示低于限值，红色表示高于限值。)你还会得到你喝得最多的那一天——如果你能记录下来的话...如果喝酒不节制，我估计什么 app 都很难用。

[![BoozeTracker Statistics](../Images/0f5c6dd4ad91385a8252b449052d8951.png "BoozeTracker Statistics")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--82f84a9q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yyb1vkew2vcaqdvsnrxv.png)

大的统计页面增加了更多的细节，包括显示你饮酒趋势的图表。我记得去年我发现在 Android 中创建一个图表非常困难，这比用 Flutter 花费的时间要多得多。也许只是我经历的变化，但很可能不止如此。使用[图表库](https://google.github.io/charts/flutter/gallery.html)的学习曲线非常短。我一定会写一个更详细的帖子。

在这一点上，应用程序对我来说已经足够好了。我将花一些时间在重构上，因为我太关注特性了。虽然我通常喜欢 TDD，但我还没有花时间熟悉用 dart 和 Flutter 实践它的方法，所以我将发现如何添加测试。

然后我就要考虑出版了。如果我想出版它，我必须做一些额外的修改。

*   实现自动同步
*   添加英制/公制单位设置
*   优选地，我必须添加逻辑来预填充数据(比如基于名称的酒精等级)

我给自己留了时间，直到九月底。到那时，我会接手另一个小项目。

*本文最初发表在[我的博客](http://sandordargo.com/blog/2018/09/12/my-first-flutter-app)上。*