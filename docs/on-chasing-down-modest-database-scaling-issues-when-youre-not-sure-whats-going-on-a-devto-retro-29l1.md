# 当您不确定发生了什么时，追踪适度的数据库扩展问题

> 原文：<https://dev.to/ben/on-chasing-down-modest-database-scaling-issues-when-youre-not-sure-whats-going-on-a-devto-retro-29l1>

这是对过去一天我们在 [dev.to](https://dev.to/) 经历的一些停机时间的简单回顾。

我在调查这个问题的过程中发布了这个:

[![ben](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 如何处理 active record::statement invalid PG::query canceled？

### 本哈尔彭 18 年 7 月 18 日 18 分 1 秒阅读

#help #postgres #rails](/ben/how-to-deal-with-activerecordstatementinvalid-pgquerycanceled-342l)

莱姆的回答是正确的。

[![rhymes profile image](img/557aa2636ae2993dfd13f29772ceb666.png) ](/rhymes) [ rhymes ](/rhymes) • [<time datetime="2018-07-18T16:01:10Z" class="date-short-year"> Jul 18 '18 </time>](https://dev.to/rhymes/comment/49el) 

德夫.托在执行什么 Heroku 计划？好像和 Heroku 的 Postgres 平衡器有关。

发生这种情况时，你是否尝试过用`SELECT * FROM pg_stat_activity`进行调查？

下面是这张表的细节:[postgresql.org/docs/10/static/moni...](https://www.postgresql.org/docs/10/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW)

尽管我猜如果你失去了联系，就没有时间去调查了...

最终的问题是，在我们管理的 Postgres 计划中，我们周期性地超出了最大连接数。究其原因，困难在于它会达到峰值，然后自己变得更好，与此同时，我们在关注我们的查询效率，却忽略了一些信号。

与我们当前计划相关的大多数数字都安全地低于我们的限制，当我们检查*当前连接*时，这似乎不是当时的问题。

展望未来，我们需要更好地监控和了解我们服务的各种瓶颈。这是一种设置和忘记一些工作了一段时间的事情的情况，一旦问题开始出现，我们就被一些转移注意力的事情弄得措手不及。

这些经历中的每一次都是一个巨大的学习机会，我对我和我的团队掌握我们所面临的这些适度的数据库扩展问题更有信心。

我说适度是因为在我们的规模下这是一个相当简单的情况，我们只需要确定问题。一个不好的结果是认为我们需要对我们的数据做任何特殊的事情。我们的未来没有分片或特殊的数据库管理，只是着眼于更好的系统监控。