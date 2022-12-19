# 我如何以及为什么跟踪咖啡

> 原文：<https://dev.to/jacoby/how-and-why-i-track-coffee-4lmk>

你可能在推特上看到过这个。

[![Dave is tracking his coffee intake](../Images/5702bd3cccfb2738f4b76f0b308f4590.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eUVbgVVq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jacoby.github.io//images/coffee-heatmap.jpg)

它展示了我作为咖啡爱好者的最后几年。每一杯咖啡(人类计算，所以我可能会在这里和那里)，跟踪和图表。

## 我如何追踪咖啡

它从一个数据库开始。我还在自学如何创建表格之类的东西，我创建了这个:

```
CREATE TABLE `coffee_intake` (
  `id` varchar(255) NOT NULL,
  `userid` varchar(255) NOT NULL,
  `source` varchar(255) NOT NULL,
  `cups` int(10) NOT NULL,
  `datestamp` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1 COMMENT='DB table for storing my coffee intake' 
```

Enter fullscreen mode Exit fullscreen mode

这是我第一次为 DB 键使用 UUIDs，而不是在一个`int`上使用`AUTO_INCREMENT`，所以需要做一些工作来整合它们。

我从 2013 年 7 月开始在推特上发布我的日常消费，但直到 9 月份我才开始添加图片。在这里，我主要是维护对 Twitter API 的控制，展示我可以让它发布我的消息。

我把 GitHub 贡献日历作为我的模型，用棕色代替绿色，因为那是咖啡的颜色。我通常把 R 作为一个绘图工具，而不是数据分析工具，消耗数据完全成形，随时可以绘图。

我发现`ggplot2`在日历中没有数据的地方留下了漏洞，所以我创建了一个表，只列出了自 2000 年 1 月 1 日以来的日子(选择它是因为 Y2K)，所以我可以做

```
SELECT *
FROM day_list d
LEFT JOIN coffee_intake c
ON d.datestamp = DATE(c.datestamp)
GROUP by d.datestamp 
```

Enter fullscreen mode Exit fullscreen mode

每当我不喝咖啡的时候得到一个零而不是零。

当我所依赖的系统退役时，我将数据转移给其他人，并(糟糕地)重写了 PHP 中的一些关键部分。我一直以咖啡追踪作为学习新技术的借口，现在也在考虑放在 AWS 上强迫自己学习 Node，Serverless，DynamoDB。或者 Node 和 Heroku。或者别的什么。

当我这样做的时候，我一定会把它写在博客上。

## 为什么我要跟踪咖啡

我相信我们(人类，“西方”，美国，开发者社区)与咖啡因有着… *有趣的*关系。我们可以说启蒙运动和工业革命的到来是因为英伦三岛的思想家们从啤酒中的镇静剂转向了茶中的兴奋剂。无可争议的是，“极客文化”将重点放在过度刺激自己和熬夜上，Jolt Cola 的口号是“所有的糖，两倍的咖啡因”，Thinkgeek 的“食物&咖啡因”部分有[含咖啡因的肥皂](https://www.thinkgeek.com/product/5a65/)。(以前是个事，我保证。)虽然我持批评态度，说“在某一点之后，你应该停止喝咖啡，成为一个速度狂”，但我也参与其中。

我已经决定尝试一个月的咖啡因“冷火鸡”。我读过很多关于咖啡因的东西，并决定做一个关于咖啡因的实验，来决定我自己对有史以来最大的瘾的看法。

这个月的第一天，我忘了，喝了一杯卡布奇诺。有一个社交场合，我总是有咖啡因，我又这样做了，中途想起我已经计划好这个月要有所不同。

第二天，当我们的车离开商店时，我还了一辆租来的车，在还车的路上，我在麦当劳吃了早餐，包括一大杯健怡可乐，因为那时我总是点一大杯健怡可乐。这就是习惯的作用。

第三天，我很乖。我记得我没有喝咖啡因，我没有。没关系。

第四天，我头疼。我感到恶心。我关掉了我所在区域的灯，坐在那里，几乎无法工作。我的同事看了看说:“如果你得了流感，你应该回家。”

这就是戒断开始影响我的地方。这时我意识到我有一个问题，我必须培养新的习惯。当我意识到“冷火鸡”计划行不通的时候，至少现在还不行。所以，我制定了规则。我会试着喝酒:

*   最多两杯咖啡
*   中午之前
*   在我编程的日子里，或者受益于提高性能的药物的日子里

软饮料马上就卖完了。茶不算在内。(2016 年的差距是我三月份只喝绿茶。)但是从日历热图中，你可以清楚地确定周末是什么时候，还有假期。

众所周知，我有时会违反这些规则。例如，今天是一个星期天，我在下午 2 点到 4 点之间喝着咖啡，和朋友们谈论 VPN。三杯和四杯的日子并不少见。但我尽量控制住他们。

我不知道这是我的规则的最终形式。我仍然会在周末头痛，但我还没有确定是否还有其他因素，或者是因为戒断。也许我需要制定其他规则来管理其他行为，并创建其他系统来创建反馈和责任。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。