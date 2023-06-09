# 星期五爆炸#27

> 原文：<https://dev.to/horia141/friday-blast-27-4obn>

[十年过去了，还没有人想出区块链(2017)](https://hackernoon.com/ten-years-in-nobody-has-come-up-with-a-use-case-for-blockchain-ee98c180100)——我一直在远处关注区块链/密码的事情，所以我没有足够的上下文来判断这篇文章。不过，这似乎也有道理，尤其是当说到许多在区块链基础上构建了一个或另一个系统的人并没有仔细研究他们要替换的是什么时。更重要的是，他们放弃的东西是否有任何价值——在许多情况下，他们确实有价值(奖励计划、质疑指控的能力等)。

[我们已经知道区块链的杀手级应用(2017)](https://hackernoon.com/we-already-know-blockchains-killer-apps-f2d443eba35)——一个更加乐观的概述。原来有杀手级应用，但范围小得多——数字黄金、国际支付和“令牌化”。作为第一个链接的内容点阅读。

我们所处的困境(2014)# talk-Erlang 创始人乔·阿姆斯特朗的“开创性”演讲。冷静地看待我们在给计算机编程时所面临的所有问题——从巨大的程序状态空间到机器的不可靠性和故障。

[当“最坏的”是最好的(在分布式系统中)(2015) #talk](https://www.youtube.com/watch?v=ZGIAypUUwoQ) -有某些情况下，构建一个处理最坏情况的系统实际上会使其对一般情况更好。这并不是大多数情况——构建一个规模达到 70 亿人的系统意味着一个只有几个人的普通案例处理起来会非常昂贵。但是，举例来说，构建一些能够承受组件故障的东西可能会让正常的无故障情况变得更好。例如，通过重新考虑关于同步和协调的假设并放弃它们。

[使用聚合、事件源和 CQRS 开发事务性微服务(第一部分)(2016)](https://www.infoq.com/articles/microservices-aggregates-events-cqrs-part-1-richardson) 和[(第二部分)](https://www.infoq.com/articles/microservices-aggregates-events-cqrs-part-2-richardson?utm_source=infoqEmail&utm_medium=SpecialNL_EditorialContent&utm_campaign=02092017_SpecialNL&forceSponsorshipId=1343)——首先声明:这仅适用于*大型*系统。对于大型系统的架构来说，微服务方法有很多优势。然而，有一点是丢失了，那就是处理复杂事务的能力——那些包含来自多个领域的实体(例如用户、订单和产品)的事务。本文解释了如何通过仔细构建应用程序、处理 DDD 集合、采用基于事件的方法进行数据建模(事件源)以及最终将数据查询与数据写入分离(CQRS)来获得大量数据。这需要大量的工作，但是这个架构最终比那个规模的任何东西都要干净。当然，一个人最终会获得一致性，但管理起来要容易得多。