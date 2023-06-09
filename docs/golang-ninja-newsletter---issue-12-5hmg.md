# Golang 忍者简讯-第 12 期

> 原文：<https://dev.to/jexiacom/golang-ninja-newsletter---issue-12-5hmg>

每周[马尔滕·贝泽默](https://medium.com/@veger)7 月 22 日[免费加入](https://www.getrevue.co/profile/golang-jexia/issues/golang-ninjas-newsletter-issue-9-119028?)或添加[时事通讯 rss](http://fetchrss.com/rss/5b27d6348a93f858388b4567788630470.xml)

[![](img/7972cd544e9250c65782b3f1ba394cd3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cHFBQDLx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0r4kshz09w5whrvmmtop.jpg)

通过 Maarten Bezemer 为 Golang Ninjas 提供的每周简讯，了解最新动态并领先于同行。

## 新闻

**[Go 模块登陆 Go 1.11](https://jaxenter.com/go-modules-land-go-1-11-146903.html?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)** Go 1.11(预计 2018 年 8 月发布)增加了对模块的实验支持。让我们来看看这对 Go 开发者意味着什么。

**[带着围棋模块兜风](https://dave.cheney.net/2018/07/14/taking-go-modules-for-a-spin?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

本来想在备用机上玩 Ubuntu 18.04。这给了我一个借口来尝试最近合并到 Go 1.11 开发分支中的模块特性。

[![](img/0f2720cdd2aae6ecc7fdfe57ece857f4.png)T2】](https://twitter.com/_rsc/status/1019743820299341824)

## 基本原则

**[类型在 Go 和 Python 中](https://medium.com/@geghamjivanyan/types-in-go-and-python-9aa0fbc25882?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

尽管有其他属性，编程语言还是有静态类型(例如 GO)和动态类型(例如 Python)。

**[从 Ruby 到 Golang——Ruby‘each’等价于 Go](https://medium.com/jexia/from-ruby-to-golang-ruby-each-equivalent-in-go-1a78e371c520?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

我们创建了一个包含 4 个条目的数组，然后使用每个条目，我们创建了一个循环遍历 fruits 数组的块，其中包含一个局部变量 fruit，然后我们使用 puts 打印 fruit 的值。

## 最佳实践

**[专注精准](https://www.ardanlabs.com/blog/2018/02/focus-on-being-precise.html?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

多年来，我一直被引导着编写一般化的函数，并创建一层又一层的抽象，这样就不会随着业务需求的变化而中断。

**[为 AWS 写围棋中间件](https://www.zachjohnsondev.com/posts/lambda-go-middleware/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

这篇文章假设读者对用 Go 编写的 AWS Lambda 函数有基本的了解。

[![](img/ddac93a3f01612b550a15aaea6755d89.png)T2】](https://twitter.com/chimeracoder/status/1019331017302298632)

**[在 Go 中实现内存数据结构](https://medium.com/@karanbirchahal/implementing-in-memory-data-structures-in-go-ef6ee1a88146?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

我们讨论 DHT 是如何为 hydra 实现的。我们假设读者已经知道了 Kademlia 协议。如果没有，请阅读本文以获取最新信息。

**[用 Golang 构建高性能端口扫描器](https://medium.com/@KentGruber/building-a-high-performance-port-scanner-with-golang-9976181ec39d?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

Go 有一个奇妙的多线程 API，它允许各种机会来构建高性能工具，包括我最喜欢的一个端口扫描器！

## 教程

**[PostgreSQL 监听/通知](https://tapoueh.org/blog/2018/07/postgresql-listen/notify/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

在另一个应用层维护外部缓存。我们将使用 PostgreSQL 的监听和通知功能在 Go 服务中维护一个内存缓存。

**[如何在 Go - part 2 中测试 TCP/UDP 连接](https://devtheweb.io/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

在继续之前，请确保您已经阅读了本系列的第一部分- [如何在 Go - Part 1 中测试 TCP/UDP 连接。](https://devtheweb.io/blog/2018/07/testing-net-connections-part-one/)

[![](img/a50b64b0a47dc7f4e901927c004e1b04.png)T2】](https://twitter.com/rakyll/status/1019455441300963328)

**[神童+散文:围棋中激进高效的机器教学](https://medium.com/@errata.ai/prodigy-prose-radically-efficient-machine-teaching-in-go-93389bf2d772?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

我们将学习如何教散文库识别一个名为 PRODUCT 的全新实体标签。它将代表“Windows 10”等各种品牌名称。

**[使用 JWT](https://blog.couchbase.com/authorization-with-graphql-golang-and-couchbase-nosql-using-jwt/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)** 对 GraphQL、Go 和 Couchbase NoSQL 进行授权

创建一个 web 应用程序，使用 Couchbase 作为 NoSQL 数据库，使用 web 应用程序后端，使用 GraphQL 作为前端查询技术，使用 JSON web tokens (JWT)作为授权组件。

## 工具

**[Neo4j 车手都去](https://medium.com/neo4j/neo4j-drivers-are-go-9697baf4d116?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)** 根据你来自哪里，可以说 2016 年是发生了一些相当重大事件的一年。当我回顾那一年时，对我来说最激动人心的时刻之一发生在四月，那时…

**[参赛围棋评论家](https://blog.usejournal.com/contributing-to-go-with-go-critic-88a25e162bf6?gi=10f6e367184b&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

我们已经验证了 golang/go 库，并发送了一些补丁，正在修复发现的问题。在本帖中，我们将检查建议的更改。

[![](img/7634c679372600d681fbf22b864c41dc.png)T2】](https://twitter.com/fatih/status/1019643295490871296)

**[纯 Go 小型家用互联网路由器](https://github.com/rtr7/router7?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

Router7 是一个小型家用互联网路由器的纯 Go 实现。它配备了使 fiber7 互联网连接工作所需的所有服务。

**[介绍散文 v2.0.0:带 NLP 走](https://medium.com/@errata.ai/introducing-prose-v2-0-0-bringing-nlp-to-go-a1f0c121e4a5?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

我们很高兴地宣布 prose 的 v2.0.0 版本，这是一个面向 Go 的自然语言处理(NLP)库。

**[在 Go 语言中使用 Lodash 的好处没有反思](https://medium.freecodecamp.org/the-benefits-of-using-lodash-in-the-go-language-without-reflection-1d64b5115486?gi=159b8c4dd57a&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

在使用 Node.js 的过程中，我逐渐依赖 Lodash 这个无价的工具。

[![](img/e912cd044ffd05f57e4358cbaf37eb3d.png)T2】](https://twitter.com/tbpalsulich/status/1019343917068800003)

## 科学论文

**[基于向量时钟的消息传递围棋程序的两阶段动态分析](https://arxiv.org/pdf/1807.03585.pdf?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)**

理解并发程序的运行时行为是一项具有挑战性的任务。一种流行的方法是通过向量时钟建立一个发生前 关系。T

## 谈戈朗

**[going to Go:Go 之旅的垃圾收集者](https://blog.golang.org/ismmkeynote?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

Go 特性目标和用例迫使我们重新思考整个垃圾收集栈，并把我们带到了一个令人惊讶的地方。这篇演讲描述了我们的旅程。

[![Episode 124\. nil and AST · ](img/c950eb14ea6182c39f63ffbc8f281ca0.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--jlAyLeMh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/revue/iteimg/003/240/240/thumb/cover.png%3F1532119439) ** [第 124 集。零和 AST](http://golangshow.com/episode/2018/06-17-124-en/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Golang%20Ninjas%20Newsletter)—[golangshow.com](http://golangshow.com/episode/2018/06-17-124-en/)**

第 124 集[恩]。零和 AST。额外收获:在@GopherConRu 采访@bradfitz 和 [@bketelsen](https://dev.to/bketelsen) 。

每周[马尔滕·贝泽默](https://medium.com/@veger)7 月 22 日[免费加入](https://www.getrevue.co/profile/golang-jexia/issues/golang-ninjas-newsletter-issue-9-119028?)或添加[时事通讯 rss](http://fetchrss.com/rss/5b27d6348a93f858388b4567788630470.xml)