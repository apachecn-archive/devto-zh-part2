# Github 明星！==用法:React 还在吹 Vue 和 Angular Away

> 原文：<https://dev.to/kball/github-stars--usage-react-is-still-blowing-vue-and-angular-away-2kk5>

上周，JavaScript 框架世界发生了一件相当重大的事情:Vue.js 在 Github 上的“明星”人数超过了 React.js。

> ![unknown tweet media content](img/e5925155621d091ed1d99ce3f58624f0.png)![Dan Abramov profile image](img/8410abe414a98a3109390b1a1f7377ee.png)丹阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)恭喜 [@vuejs](https://twitter.com/vuejs) 在 GitHub 上超越 React 的星数！似乎我无意中捕捉到了一个历史性的瞬间。2018 年 6 月 15 日上午 01:46[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1007439168400654336)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1007439168400654336)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1007439168400654336)

使用一个名为 [Star History](http://www.timqian.com/star-history/) 的工具，我们可以看到这个指标特别讲述了 React 的历史优势被暴涨的 Vue 超越的故事。

[![](img/180e44f2ae8d65c3b56a1bf7ed1d8a94.png)T2】](http://www.timqian.com/star-history/#facebook/react&vuejs/vue)

当框架的受欢迎程度提高时，经常会出现这种情况，这引发了关于哪个框架“真正”更受欢迎、哪个框架更强大等等的大量辩论。

### 信令 Vs 实际使用

不要误解我，我喜欢 Vue——我最近使用它的次数远远超过 React 但 github stars 更多的是衡量一个项目的“时髦度”，而不是它的实际使用情况。一个明显的例子将流行的 Apache 服务器与一个相对无聊的 JavaScript 项目进行了比较:

> ![unknown tweet media content](img/0b46094b08352c89693efd33a26299ed.png)![Ben Sandofsky profile image](img/6557edeca17ce315b201eb9ffc236ae7.png)本·桑多夫斯基@桑多夫斯基![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)一个为 38%的互联网提供动力的网络服务器在 GitHub 上拥有 1766 颗星。为节点控制台添加颜色的库有 3206 个。2018 年 6 月 15 日 20 点 53 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1007727882095886336)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1007727882095886336)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1007727882095886336)

为了更好地了解 Vue 与 React 的相对受欢迎程度，我们需要一些方法来衡量项目的实际使用情况。

### 使用 NPM 下载作为代理使用

没有完美的方法——有许多方法可以安装和使用这些项目——但是 NPM 已经成为安装 JavaScript 包的事实上的标准。

如果我们查看这些框架的核心包——`vue`包和`react`包的下载统计数据，这将为我们的实际使用提供一个合理的代理。

为此，我使用了一个名为[npm-stat.com](https://npm-stat.com)的网站，它可以让你生成从 2015 年 2 月开始的 NPM 软件包的历史下载图表。(这可以追溯到 NPM 注册中心报告的下载数据)。

### 数据:React 依然独霸

查看过去 2 年的数据，我们可以看到，与 github stars 数据形成鲜明对比的是，React 在 NPM 包下载量衡量的实际使用方面仍然占据主导地位。

[![](img/563c78a6166664ecae4eb9eec1785d07.png)T2】](https://npm-stat.com/charts.html?package=react&package=vue&from=2016-06-01&to=2018-05-31)

这并不是说 Vue 在采用率方面表现不佳——事实上，Vue 在倍数方面略有赶上，从 2016 年 6 月的约 12:1 比例(1.693 万比 14.15 万)到 2018 年 5 月的略低于 7:1 比例(9.388 万比 1.374 万)。但是在支持 React 方面仍然存在巨大的使用差距。

### 添加棱角

虽然上周的讨论主要集中在 React 和 Vue 上，但我们也来看看“三大”框架的另一个成员 Angular。

由于 Angular 社区或多或少地分成了“Angular.js”和“Angular”(版本 2 和更高版本，使用 typescript)，我们将把这两者都添加到我们的查找中。

首先，就 github 明星而言:

[![](img/12ba2035d50806f2750b71e0e51e79c3.png)T2】](http://www.timqian.com/star-history/#facebook/react&vuejs/vue&angular/angular.js&angular/angular)

我们可以看到 Angular.js 比 React 和 Vue 领先了一点，但在 2016 年年中的《明星力量》中被 React 超过，在 2017 年被 Vue 超过。第二次迭代 Angular 自 2016 年 9 月推出以来，一直在逐渐赶上 Angular.js，但在这一受欢迎程度方面仍然排名第四。

### 棱角分明的 NPM 下载

从 Angular 的两个变体的用法而非“明星效应”来看，又是另一个故事。

[![](img/a409810da1187e5dcea43dc309691de0.png)](https://npm-stat.com/charts.html?package=react&package=vue&package=%40angular%2Fcore&package=angular&from=2016-06-01&to=2018-05-31)

*其中的‘angular’包是原来的 Angular.js，而@angular/core 是新 Angular 的核心。

在使用方面，新的 Angular 从零开始飙升，在发布后立即超过了 Vue 和 Angular.js，并一直保持着领先地位，尽管没有做出任何反应。

在这段时间里，Vue 从 2016 年 9 月 Angular.js 或 Angular 的大约 1/3 的下载量，到 2018 年 5 月勉强超过 Angular.js 并达到 Angular 的 1/2 的下载量。

### 总之:React 仍占主导地位，Vue 增长最快

通过挖掘 NPM 的下载统计数据，我们发现尽管围绕 Vue 的 github 明星暴涨进行了大肆宣传，React 仍然是 JavaScript 框架领域中 800 磅重的大猩猩。它即将突破每月 1000 万次的下载量，并且一直在以惊人的速度增长。

也就是说，Vue 粉丝没有理由难过。在过去的两年里，Vue 的增长速度比其他任何主流 JavaScript 框架都要快，最近赶上了 Angular.js，并逐渐缩小了它与 Angular 和 React 之间的差距。

* * *

附:如果你对这类话题感兴趣，你或许应该[在 Twitter 上关注我](https://twitter.com/kbal11)或者加入我的邮件列表。我发出一份名为“[周五前端](https://zendev.com/friday-frontend.html)”的每周简讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T4】