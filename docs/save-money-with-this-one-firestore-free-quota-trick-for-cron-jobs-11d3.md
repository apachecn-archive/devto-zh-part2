# 节省金钱，这一个 firestorm 免费配额的 cron 工作

> 原文：<https://dev.to/supercilex/save-money-with-this-one-firestore-free-quota-trick-for-cron-jobs-11d3>

对不起，但我就是无法抗拒这个扣人心弦的标题。😛玩笑归玩笑，假设你有一个每月的 CRON 工作，清空你的用户的垃圾，并做一些其他 Firestore 相关的清理工作。这是 Firebase 云功能的一个很好的用途，但它是有代价的:你要一次性创建/更新/删除文档。

请记住，Firestore 免费配额每天重置。因此，如果你太小而不能每天用完你的免费配额，但又太大而不能留在 Spark 计划中，你应该考虑改为运行*每日*工作。

只要你的云函数只查询它们将要处理的数据，那么每天运行一个作业并充分利用这些免费配额就会便宜得多。作为一个额外的奖励，你将能够更好地预测你每月的账单，因为你将每天都使用 Firestore。

[![Chris Pratt Happy](img/f029f9ce135d5a9483be79ade8ec1f97.png)T2】](https://alexsaveau.dev/assets/chris-pratt.gif)

## 运行日常工作

运行每日 CRON 作业简单得可笑:只需创建一个[云调度器](https://cloud.google.com/scheduler/)作业，设置为每天凌晨 3 点(`0 3 * * *`)运行，发布一个空数据负载(`{}`)到您选择的主题(`daily-tick`)。它看起来会像这样:

[![GCP Scheduler Daily Tick](img/bf97bc9757e27edb68cf9dac3b940145.png)T2】](https://alexsaveau.dev/assets/firebase/daily-tick.webp)

这个滴答是完全免费的！

在函数的索引文件中，订阅`daily-tick` pubsub 事件，就可以开始了。

* * *

希望这能帮你节省一些钱。编码快乐！