# 周五爆炸#42

> 原文：<https://dev.to/horia141/friday-blast-42-8nb>

[在脸书(2018 年)](https://blog.acolyer.org/2018/06/06/reducing-dram-footprint-with-nvm-in-facebook/) -晨报评论了脸书的一篇关于他们在 MySQL 数据库中使用 NVM 的新论文。非常有趣的东西和非易失性存储器的冷静的生产使用。

[决策者和哲学家小说家的量子计算(2018)](https://www.scottaaronson.com/blog/?p=3848)-Scott Aaronson 关于量子计算对*社会意味着什么的好东西*。许多人关注密码学，但是机器学习和优化以及量子化学的模拟更重要。

[ServiceFabric:在云中构建微服务的分布式平台(2018)](https://blog.acolyer.org/2018/06/05/servicefabric-a-distributed-platform-for-building-microservices-in-the-cloud/)——又是一篇晨报评论，这次是微软的一个系统。这非常令人印象深刻——有点像服用了类固醇的 Kubernetes，内置了许多高可用性&路由。不过，它不是 PaaS 或类似的东西，只是一个服务调度程序。

[网飞的全周期开发者——运营你所建造的(2018)](https://medium.com/netflix-techblog/full-cycle-developers-at-netflix-a08c31f83249)——网飞的目标是他们的开发者是“全周期的”(我想象这是*_ 全栈*的延伸)。这意味着他们负责构建*和*运行服务。事实上，这在大型互联网公司中很常见——亚马逊、谷歌、脸书等使用它，SREs 做更多的基础设施层面的工作(例如监控作为服务提供的数据库)。我发现对这种方法的利弊的分析也很有趣。有些人真的想做某一类型的事情，并且会被整个周期的期望所挫败。不过，说实话，那总是发生在那些深入人心的人身上。如果你在这些公司中的一家公司构建编译器，是的，你可能会对监控服务的期望感到恼火。但是，最有可能的是，您不会处于引导的位置——您正在构建编译器。其他人确实喜欢看*整条香肠被制作出来*(包括我自己)。

[写一本技术书的经济学(2018)](https://medium.com/@rothgar/the-economics-of-writing-a-technical-book-689d0c12fe39)——一个真正有见地的观点，看看写一本书需要什么，以及什么是*典型的成功结果*。我们不是在谈论大家都知道的巨大成功——TAOCP 之类的。只是一本关于当前科技的普通书籍，相对较短，保质期适中。也没那么好。投入的时间是巨大的(相当于一年的兼职工作)，但回报却是微乎其微。然而，写一本书可能是一种巨大的自我提升。