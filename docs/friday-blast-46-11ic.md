# 星期五爆炸#46

> 原文：<https://dev.to/horia141/friday-blast-46-11ic>

[为什么 Kubernetes 是新的应用服务器(2018)](https://developers.redhat.com/blog/2018/06/28/why-kubernetes-is-the-new-application-server/)-K8s 作为构建应用的新“企业”事物的案例。来自 RedHat、makes 或 JBoss 和其他企业级的东西(以及一些不那么企业级的东西)。

[我们如何使用 Go (2018)](https://eng.uber.com/go-geofence/) 构建优步工程每秒最高查询服务——这是一篇关于优步境内 *geofence* 服务的有趣阅读。这些数字令人印象深刻——10 万 QPS 是巨大的。当然，这都是内存服务，但即使这样，Go 的性能也是一流的。有趣的是，只使用了基本的锁定来构建这个东西，因为团队发现更高级的方法是复杂的，而且不需要。

[如何不构建你的数据库支持的 web 应用(2018)](https://blog.acolyer.org/2018/06/28/how-_not_-to-structure-your-database-backed-web-applications-a-study-of-performance-bugs-in-the-wild/)——晨报评论[杨等](https://hyperloop-rails.github.io/220-HowNotStructure.pdf)的一篇论文。主要的一点是关于程序员使用的 ORM 中的小调整如何可以获得巨大的性能提升。他们应用的中间值只有 5 行。我认为把这些事情做好的难度是像 Dapper 或 Knex 这样的微体流行的原因之一。

[对越来越多的人来说，工作似乎毫无意义(2018)](https://news.ycombinator.com/item?id=17260911)——hacker news 对《纽约客》上一篇有趣文章的讨论。两者都值得一读。我的观点是，我们将在未来看到很多自动化，狗屁工作或最低收入计划将是两种现实的前进方式，不会导致彻底的反乌托邦。

[你作为程序员的进步全靠你自己(2014)](http://thecodist.com/article/your_progress_as_a_programmer_is_all_up_to_you)——至理名言。雇主、同事、客户、专业协会等。不会在乎你的未来。但是我们的行业对那些没有跟上潮流的人来说是非常无情的。不过话说回来，大部分职业都是这样的。医学、法律等。不要站着不动。