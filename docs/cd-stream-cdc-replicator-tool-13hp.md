# CD 流:CDC 复制器工具

> 原文：<https://dev.to/sathyasarathi90/cd-stream-cdc-replicator-tool-13hp>

#### ++ ETL 管道上的 Cons

只是工作场所的又一天；

***开机 5 分钟后:***

您听到每个人都抱怨生产数据库很慢。你很快开始调查；探索仪表板上所有可能的结果...

> **会不会是您向生产支持部门提出的要解决的长时间运行缓慢的查询？或者是基于非索引列运行的查询之一？**

[![](img/6b219d338301ef82630dde42e8aaf39f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BCZFJnIB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/0%2A5b7aE4SKkbTm-jb8.gif)

***第 6 分钟 15 分钟后:***

接下来，你会听到数据分析师同事为他们失败的报告而哀叹。

您现在意识到您的 CPU 已经承担了大量的查询负载，并且您理解您的关系数据库系统已经进入了一个永恒的休眠状态。

### 而这一切都是因为您的 ETL 管道查询运行缓慢..！！丁。钟声...丁…！！我们有赢家了！！！

好吧，让我们倒回去一点。

您可能做过/使用过以下其中一项:

*   SELECT * from production _ database . table where updated _ at 介于 x 和 y 之间；

*   每隔几分钟批量导出和转储一次

*   长期运行和被遗忘的僵尸 Crontabs

[![](img/153a6c6a8b4d3623939f1b276165a592.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dH1CZBKE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/311/0%2ANWQ-pURqbOWtGPdr.jpg)

让我们这么说吧...数据库大小< 500 GB; it’s OK to do selects but if it exceeds > 500GB，除非您削减预算，否则永远不要进行批量选择，并以管道的形式通过网络将其传输到目标数据库。

#### 进入疾控中心:

如果您的数据在快速增长，如果您的 BI/BA 需要访问那个甜蜜的 DWH(数据仓库)，CDC 也称为“变更数据捕获”;这就是我们要走的路。

#### 你是说复制？—我不想被我的 OLTP 数据库引擎困住:

你并不孤单。出于许多原因，数据争论者通常不喜欢与 OLTP 类似的数据库引擎。主要原因包括查询性能、对触发器的需求以及重新运行转换作业的便利性。但是，这样做需要设置气流集群并设置连接器来转换和加载。

[![](img/5a6907dcb65368e57e7b84d9e0d38791.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nvyGd-au--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/0%2AS7iFWzeO5Ts9cYJ2.jpg)

如果您可以在不从生产数据库中进行批量选择的情况下完成所有这些工作，那不是很好吗？当然，你可以...

**CD-Stream** 是一个跨数据库 CDC 驱动的复制器工具，目前支持 ***MySQL 和 Postgres*** 之间的复制。

该工具运行队列来处理源数据库的二进制日志中出现的信息，并将其复制到完全不同引擎的目标数据库中。

[![](img/531417efd35d2916bd2a7aea76887ba9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ctdh6Uir--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/0%2AzO-ZxOZB5LpJtGsY.png)

发布设置，如项目页面所给:[CD-Stream](https://github.com/datawrangl3r/cd-stream)；项目中有一个名为“sample”的目录，其中包含一些密集的 DDL 和数据插入脚本，供您评估和练习。

还有那个，*人*；就是你争论生产数据的方式！！！

*原载于 2018 年 10 月 30 日*[*https://www . data wrangler . in*](https://www.datawrangler.in/2018/10/cd-streamcdc-replicator-tool-cons-on.html)*。*

* * *