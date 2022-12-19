# 星期五爆炸#55

> 原文：<https://dev.to/horia141/friday-blast-55-g3a>

[感谢你的帮助，NoSQL，但我们已经从这里得到了它(2018)](http://blog.memsql.com/nosql/) -越来越多的公司意识到 SQL 可以很好地处理非常大的规模，并且失去了 ACID、Schemas、SQL 等。在大多数情况下，为了 NoSQL 解决方案提供的一点点好处，难道不值得付出这样的代价吗？一个很好的引用“不清楚为什么图式作为 NoSQL 运动的一部分丢失了。”-我自己也常常想知道为什么。

[为什么我们从 MongoDB 迁移到 PostgreSQL (2018)](https://dzone.com/articles/why-we-moved-from-nosql-mongodb-to-postgresql) -与上一个相同，尽管一些关于迁移的推理会影响它们。

[再见微服务:从 100 个问题儿童到 1 个超级巨星(2018)](https://segment.com/blog/goodbye-microservices/)-Segment 如何将他们的数据分发服务从每个目的地一个微服务转移到所有目的地的单个整体服务。尽管工程上的推理是可靠的，而且看起来*确实是这样*将服务分成微服务从一开始就是不明智的。我认为这是一个警示性的故事，如果你要拆分成服务的东西真的那么独立的话，就要认真思考。从技术上来说，每个函数调用都可以调用另一个服务，但这太疯狂了。

[YAML:也许并不那么伟大(2016)](https://arp242.net/weblog/yaml_probably_not_so_great_after_all.html)——显然 YAML 很大——甚至比 XML 还大。我*不知道*。表示对象的其他复杂性也是如此。奇怪的是，经过 70 多年的计算，仍然没有一种通用的方式来表示配置，这种方式易于人类阅读，易于计算机解析和发出，并且与当时的编程语言很好地集成。

[使用 Archer 简化 Neflix 的媒体创新(2018)](https://medium.com/netflix-techblog/simplifying-media-innovation-at-netflix-with-archer-3f8cbb0e2bcb) -其中网飞详细介绍了他们的 Archer 系统。这是一个为分析和处理视频量身定制的大数据平台。它旨在使事情变得简单，并且内置了各种媒体处理功能。你会得到一个类似 MapReduce 的界面，视频帧甚至场景都已经为你分割好了。还有更多的细节，但我认为这是一个很好的例子，说明了建立这样一个工具可以使许多其他团队做他们以前不能做的事情，因为其中涉及的复杂性。