# 2018 年 6 月 15 日星期五的学习周。

> 原文：<https://dev.to/threedeeprinter/the-week-in-learning-for-friday-june-15th-2018-33pa>

作为我本周做的一个技术筛选项目的一部分，我必须创建一个能在谷歌地图上显示 10，000 个标记的 web 应用程序。每个坐标必须随机生成，并落在特定的纬度和经度范围内。每个标记必须有基于另一组范围的特定颜色。屏幕必须按颜色显示标记的总数。最后，你需要能够打开和关闭标记。

接下来的问题是:这必须发生在 SalesForce 中。

是的，我的朋友们，我参加了 Apex 脚本和使用 VisualForce 生成网页的速成班。我还必须学习谷歌地图应用编程接口。

我从我所知道的开始，并决定使用 JSFiddle 编写 Javascript，然后当我对此感到满意时，我会将它转移到 SalesForce。

当我开始这个项目时，我想在地图上显示 10，000 个标记。我是说那有什么意义？当我终于能够让它渲染，这是它看起来像什么。我笑了，因为这一切都点击到位。

[![Google Map of North America with 10,000 Markers](../Images/3c02c3825172370046eedcecd42f40cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HiixWaXu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/naxwpvlih5gk0v7roj50.PNG)

因此，我必须创建自定义 SalesForce 对象，使用 Apex 脚本在 SalesForce 中生成坐标，将 Html 和 Javacript 传输到 VisualForce 页面，然后从创建的对象中获取数据。请记住，在开始这个项目之前，我从未使用过 SalesForce。

我只是勉强成功。问题是 VisualForce 的 RemoteObjects 功能最多只能获取 100 条记录。我无论如何也弄不明白如何通过执行多个请求和分块数据来获取所有记录。

那么我学到了什么？

销售队伍和两个相关的 API(Apex 脚本，Visual Force)
谷歌地图 API。

这周你学到了什么？