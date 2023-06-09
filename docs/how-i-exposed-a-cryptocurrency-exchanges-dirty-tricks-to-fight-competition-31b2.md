# 我是如何揭露一家加密货币交易所对抗竞争的肮脏伎俩的

> 原文：<https://dev.to/leovarmak/how-i-exposed-a-cryptocurrency-exchanges-dirty-tricks-to-fight-competition-31b2>

难怪在 2017 年牛市之后，加密货币几乎为世界各地的每个人所知🌏。这种情况吸引了更多的玩家进入市场。从试图轻松赚钱的人到迎合这种突然激增的用户的投资者，从机构玩家到菜鸟用户。许多被称为加密交易所的新平台开始涌现，人们可以在这里买卖加密货币..截至 2018 年 4 月，[Bitcoin.com](https://news.bitcoin.com/the-number-of-cryptocurrency-exchanges-has-exploded/)报道称，大约有 500 多家加密货币交易所。由于交易所的数量突然激增，这些交易所采取各种策略将更多用户聚集到他们的平台上，从竞争到空投和推荐计划。

* * *

[BitBns](https://bitbns.com/) 是印度流行的加密货币交易所之一。从 2017 年 12 月开始运营。尽管他们是这个市场的新手，但他们并没有花太多时间就获得了良好的声誉，这要感谢那些已经在运营一家好公司的创始人，以及由于市场低迷而不断增长的用户群📈

他们一开始就疯狂上市。他们拥有的数字资产比 Koinex、T2、Coinome 甚至其他流行的交易所都多。人们喜欢比特币是因为它们支持迅速，而且提供了更多的加密货币选择。

但是 2018 年 Q1 奥运会后，市场上有许多交易所，所有这些交易所都在使用各种可能的伎俩，从交易竞争到推荐计划，以获得市场份额。

影响用户选择交易所的因素有:

*   **24 小时交易量**(人们不想等待很长时间来执行他们的订单)
*   **良好的支持**(人们不喜欢等待，因为加密货币波动性很大，他们期待快速支持)
*   **更多列表**(人们喜欢购买各种不同的加密货币)

*   **24 小时交易量**对于任何加密货币交易所来说都起着非常重要的作用，因为 [CoinMarketCap](https://coinmarketcap.com) 不会列出交易量不超过特定阈值的交易所。CoinMarketCap 排名对所有交易所都非常重要，因为它就像所有加密资产或交易所的搜索索引一样。从本质上讲，CMC 就像是加密资产和交易所的谷歌。

事实上，像我这样的交易者会先查看 CMC 上的交易量，然后去各个交易所。现在回到 BitBns，除了容量，它已经拥有了所有其他东西。也许正因为如此，他们决定操纵它，只是为了获得市场份额。

* * *

### **解码**:

我过去在大多数印度交易所交易过，BitBns 的交易量没有那么大。然后当 BitBns 突然有了比 WazirX、Koinex、Coindelta 更好的成交量时，我以为是系统 bug。过去，BitBns 被指控伪造卷册，他们解释说系统有缺陷。这甚至在 2018 年 3 月被 [Coincrunch](https://coincrunch.in) 报道。

> ![](img/2dcc08c517261771c5938295ee8f75be.png)![](img/2b6a6717c88fbe0570e92ff47fd022dd.png)Coin Crunch India@ Coin crunchin![](img/4d9c44713c216584b3d48ff3455cbb68.png)我们调查了一位用户举报的关于 BitBns 上 ETN 卷的问题。
> 
> 我们发现 BitBns 错误地计算了交易量。在大多数情况下，贸易量会翻倍。
> 
> [@bitbns](https://twitter.com/bitbns) 请回复
> 
> [【加密货币】](https://twitter.com/hashtag/Cryptocurrency) [【交易】](https://twitter.com/hashtag/Trading)[【交易】](https://twitter.com/hashtag/Exchange)[【bit bns】](https://twitter.com/hashtag/Bitbns)[【调查】](https://twitter.com/hashtag/Investigation)下午 12:51-2018 年 3 月 17 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=974991522067337221)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=974991522067337221)

现在到了 2018 年 7 月，当大多数印度交易所的交易量急剧下降时，BitBns 的交易量仍然很好。我对此心存疑虑，想核实一下是否属实。据我所知，实际上显示的体积是错误的。

首先，我看了看**交易历史**是如何在网站上更新的。本质上，交易历史包含了最近十次交易的历史。它显示一个时间戳，数量和 PPU(单价)。

[![trade history](img/a25e672794767aef663c522b26e682bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PQNGJRRM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2AgV3B4mDwYTGo-zR86L3Spg.png)

后来，我从 XHR 的请求中发现，交易历史数据是使用轮询 API 发送的。

有两个 API。一个用于生成会话 ID，另一个使用这个**会话 ID** 获取交易历史数据。

由于这个原因，我决定查看一下 **BTC** 和 **XRP** 市场的交易量，因为它们在 BitBns 上的交易量排名前五。现在我知道如何获取数据，并且必须决定我的堆栈。

我非常熟悉 **Node.js** ，并决定使用它将我获取的所有数据放入数据库。

在那之后，我决定使用 MySQL，因为它对我来说是最合理的。我使用 MySQL 大概是三年前的事了，每当我告诉我的朋友我使用 MySQL 时，他们都会笑😹

我使用 axios 从 API 获取数据。然而，我收到的响应是如此糟糕，我不得不清理响应，并把它变成可解析的 JSON。

我整理了所有东西来获取数据，然后必须将所有东西存储在 MySQL 中。我用这样一种方式编程，当程序启动时，它会在第一次运行时将数据放入数据库中。然后，它将数据库中的最新记录与从交易历史中新获取的数据进行比较。如果根据从 API 获取的数据中的时间戳检测到新记录，那么它将被插入到数据库中。我为 XRP 和 BTC 各创建了一个表，其中包含 SNo、时间戳、容量和 PPU 列。

数据在 MySQL 内部是这样存储的:
[![mysql store](img/7144d726ac59c9e2c6860ee0baf266de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4WN31JsQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AEC5ijlLA23AvExpb7OT33w.png)

如果数据的时间戳、卷和 PPU 相同，则该数据被视为重复数据，不会存储在数据库中。这种情况非常罕见，但我只是以防万一。

我使用邮件服务在脚本运行时发送通知，并在 24 小时后直接将总量发送到电子邮件。在那之后，我把所有东西都放在一个 setInterval 中，以保持获取和存储数据。

我使用的不同功能概述:
[![Fetching new data using setInterval](img/76633f93591b95b57d90ef259f15b376.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FQdOjZaz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AyvOxwr21coRrqGfGFvXMjw.png)

由于时间间隔很长，你可能会认为我们会错过一些交易。然而，在印度市场，这实际上是一个很大的更新率。这是因为在币安没有很多交易。尽管如此，我没有漏掉任何数据。
我已经完全准备好了我的代码并部署它。

> 我把我的程序部署在了 WeDeploy 和 Google 云平台上的 MySQL DB 上。多亏了 WeDeploy 出色的团队，部署和基础设施根本不成问题。

* * *

### **结果**:

我在程序开始 24 小时后停止了它。尽管我们在电子邮件中获得了汇总结果，但我决定自己在 MySQL 中汇总它们。以下是该计划的结果。

以下是基于我们程序的 XRP 和 BTC 市场 24 小时累计成交量:
[![volume mysql](img/c497182550df497b139623aef3cb888b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hHIipdb0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A6VCNxNHV3qVV5ygMdZ23ng.png)

BitBns 和 CoinMarketCap
在 **BitBns** 和 **CoinMarketCap** 报告的交易量之间存在巨大差异:

XRP 市场(右)和 BTC 市场(左)的 24 小时成交量:
[![difference](img/149ae3f9a59cf7135c8da0aeb968a486.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mK9grZmL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AW4apr9vb0CYq15XfDROQTw.png)

24 小时后 CMC 上显示的卷:
[![difference](img/d3c4ead68207077cb62f5d9d14697605.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--He3cqYmw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AdQYum_eNIiM58pPP1LbpWg.png)

BitBns 和 CoinMarketCap 上显示的交易量称为滚动交易量。意思是当你打开他们的网站，网站上显示的成交量是过去 24 小时的。

> 如果您在某个时间`T`打开它，那么显示的音量来自于`T-24`。

在 BitBns 网站上，XRP 的 **24 卷显示为 375410，BTC 的 24 卷显示为 20.266** 。
让我们把这个和我们程序的结果比较一下。

*   程序显示 XRP 的 24 小时访问量是 62，365，而在 BitBns 上是 3，75，410。网站上报道的数量比实际数量多 500%。
*   同样，根据我们的程序，BTC 的 24 小时交易量是 2.638，而在 BitBns 上显示是 20.266。网站上报告的数量比实际数量多 668%。

> ***BitBns 正在报告一个被操纵的 24 小时 holumes。据报道，与原始数据相比，XRP 的数据增加了 501%，BTC 的数据增加了 668%。*T3】**

* * *

我们可以清楚地看到，BitBns 正在伪造其交易量，以获得相对于其他交易所的竞争优势。我们不知道许多其它交易所报告了虚假交易量。

特别感谢 [MCD-50](https://github.com/MCD-50) 帮我解围。

如果你想证实我的说法，这里有在 GitHub 上的项目链接