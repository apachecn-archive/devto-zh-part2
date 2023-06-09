# 一部电影——探索电影数据库 Api

> 原文：<https://dev.to/robertoissc/one-movie---exploring-the-movie-database-api-1g4p>

我不知道我是否符合电影爱好者的定义，但我真的很喜欢看电影，我妻子也是，我们每周都去看电影，已经有 4 年了，空闲时间我们会在家多看几场。

[![](img/a283cc16bd95da9ea85c7586e029d613.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uBzGsoer--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.giphy.com/media/pUeXcg80cO8I8/giphy.webp)

正因为如此，问题:“我们该看什么电影？”对我们来说已经成为一个反复出现的问题，所以我做了任何其他软件开发人员都会做的事情:我开始编码。

我马上发现了这个非常酷的 API:[https://api.themoviedb.org](https://api.themoviedb.org)

[![movie_logo](img/829b0f610c15954c9fc1a732b90e5a0d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_B33-TRT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://isscroberto.files.wordpress.com/2017/12/movie_logo.png)

一个探索电影数据库的公共 api，提供了大量很酷的特性和数百万部电影的大量信息。厉害！

[![](img/6018be5662516175fc5d70d907dae2d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b_ZaigBe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.giphy.com/media/d2Z9QYzA2aidiWn6/giphy.webp)

我使用了/discover endpoint，你可以用它来发现按许多参数分类和过滤的电影，如评分、投票、流派等。你可以在这里看规格[。](https://developers.themoviedb.org/3/discover/movie-discover)

在花了几个小时探索 api 和几天空闲时间编写代码后，我有了 [One Movie](https://play.google.com/store/apps/details?id=com.isscroberto.onemovie) ，这是一个非常简单的应用程序，当你发现很难决定看哪部电影，而你需要一个为你做出决定的应用程序时，它会为你提供随机电影。

[![logo_round](img/441668ec6df7ea4b5009d224a4239933.png)T2】](https://play.google.com/store/apps/details?id=com.isscroberto.onemovie)

这就是你得到的。

该应用程序会将你直接带到主屏幕，在那里你会自动获得一部随机电影，你可以看到一些基本信息，如果你对当前电影不满意，还可以点击**刷新**获得下一部电影。

然后是过滤屏幕，您可以在其中缩小搜索范围，它有以下选项:

*   流行的

流行度是 IMDB 中一个非常重要的指标，它考虑了许多参数，如当天的投票数和浏览量，喜欢该电影并将其添加到其观看列表中的用户数，上映日期，投票数等。[一部电影](https://play.google.com/store/apps/details?id=com.isscroberto.onemovie)按人气排序，取前几部。

*   在剧院

很简单。它过滤上映日期不到一个月的电影和影院上映的电影。

*   年

它过滤发行年份等于或大于指定年份的电影。

*   初始语言

它通过选择的语言过滤电影。

*   类型

它过滤电影，将搜索限制在所选类型的电影。感觉像看喜剧？恐怖？浪漫？

*   投票

它过滤投票平均值等于或大于指定值的电影。

当然，[有一部电影](https://play.google.com/store/apps/details?id=com.isscroberto.onemovie)有一个简单的关于屏幕。就是这样。

[![Screenshot_20171226-222728.png](img/4be8dde3f329e157144e6cd64702ca91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--88lNHTI4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://isscroberto.files.wordpress.com/2017/12/screenshot_20171226-222728.png%3Fw%3D586)

看看它:)留下评论，我可以如何改善它，并在 Play Store 评级将是真棒。

查看[回购](https://github.com/robertoissc/OneMovie-Android)并随意投稿。