# 掌握 Golang 的世界-第 12 期

> 原文：<https://dev.to/jexiacom/master-the-world-of-golang---issue-12-5cfn>

每周[马尔滕·贝泽默](https://medium.com/@veger)7 月 22 日[免费加入](https://www.getrevue.co/profile/golang/issues/master-the-world-of-golang-issue-9-120281?utm_campaign=Issue&utm_content=view_in_browser&utm_medium=email&utm_source=Master+the+World+of+Golang)或添加[时事通讯 rss](http://fetchrss.com/rss/5b27d6348a93f858388b4567878277344.xml)

[![](../Images/8d074d9e68f59ef805ce7087574ae795.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oNBWwDlc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8iykftgune5q747g07p5.jpg)

掌握围棋世界。每周 Maarten Bezemer 都会提供关于理论、趋势、工具和主题的最佳文章。

### 

[Maarten](https://github.com/veger) 使用 Go 搭建开发者平台后端 [Jexia](https://www.jexia.com/) 。你是高级用户吗？用他的 [Golang 忍者简讯](https://www.getrevue.co/profile/golang-jexia)保持领先。

* * *

## 新闻

**[Go 模块登陆 Go 1.11](https://jaxenter.com/go-modules-land-go-1-11-146903.html?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)** Go 1.11(预计 2018 年 8 月发布)增加了对模块的实验支持。让我们来看看这对 Go 开发者意味着什么。

**[带着围棋模块兜风](https://dave.cheney.net/2018/07/14/taking-go-modules-for-a-spin?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

本来想在备用机上玩 Ubuntu 18.04。这给了我一个借口来尝试最近合并到 Go 1.11 开发分支中的模块特性。

[![](../Images/0f2720cdd2aae6ecc7fdfe57ece857f4.png)T2】](https://twitter.com/_rsc/status/1019743820299341824)

## 基本原则

**[建筑去向来源](https://carolynvanslyck.com/blog/2018/07/building-go-from-source/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

在我们成为 Go 贡献者之前，第一步是能够从源代码中构建 Go。

**[类型在 Go 和 Python 中](https://medium.com/@geghamjivanyan/types-in-go-and-python-9aa0fbc25882?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

尽管有其他属性，编程语言还是有静态类型(例如 GO)和动态类型(例如 Python)。

**[Goroutines on 归并排序](https://medium.com/@yliu224/goroutines-on-mergesort-12a2a7a43cc2?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

我不得不使用多线程来实现排序算法之一*桶排序*、*合并排序*和*双字节排序*。我使用 Goroutines 来实现合并排序。

**[GoLang](https://medium.com/@anshujhaa/beginners-guide-to-testing-in-golang-cd33b025dafe?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)初学测试指南**

我最近开始学习 GoLang，并花了一些时间学习 GoLang 中的各种测试概念。

## 最佳实践

**[写一个简单的 REPL 在走](https://medium.com/@diego_pacheco/writing-a-simple-repl-in-go-24a274228954?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

我想展示在 Go 中编写一个简单的 REPL 是多么容易。REPLs 在 Scala、Clojure 和 Haskell 等函数式编程社区中非常流行。

**[基本 Redis 示例同](https://medium.com/@gilcrest_65433/basic-redis-examples-with-go-a3348a12878e?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

Redis 很棒。根据最近的堆栈溢出调查，它连续第二年成为最受欢迎的数据库。

[![](../Images/ddac93a3f01612b550a15aaea6755d89.png)T2】](https://twitter.com/chimeracoder/status/1019331017302298632)

**[为 AWS 写围棋中间件](https://www.zachjohnsondev.com/posts/lambda-go-middleware/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

这篇文章假设读者对用 Go 编写的 AWS Lambda 函数有基本的了解。

**[从 Ruby 到 Golang——Ruby‘each’等价于 Go](https://medium.com/jexia/from-ruby-to-golang-ruby-each-equivalent-in-go-1a78e371c520?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

我们创建了一个包含 4 个条目的数组，然后使用每个条目，我们创建了一个循环遍历 fruits 数组的块，其中包含一个局部变量 fruit，然后我们使用 puts 打印 fruit 的值。

## 教程

**[使用 Golang 读取文件](https://golangbot.com/read-files/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)** 学习如何读取整个文件，以及如何使用 Go 读取小块文件。本教程还讨论了在 go 二进制文件中捆绑静态文件。

**[如何编写可测试的 Golang 代码](http://forgelib.com/lib/17Ppqko9UDD9h9PQs45wZTIlldq?How%2Bto%2Bwrite%2Btestable%2Bgolang%2Bcode&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

在本文中，重点是如何编写可测试的代码，以及如何屏蔽掉一些不相关的操作。

[![](../Images/a50b64b0a47dc7f4e901927c004e1b04.png)T2】](https://twitter.com/rakyll/status/1019455441300963328)

**[GoLang 地图入门:教程](https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/Get-started-with-GoLang-maps-A-tutorial?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)** GoLang 是一种越来越流行的编程语言，具有独特的数据操作方法。这里我们来看看 GoLang 地图是如何编程的。

**[在你的家庭实验室里跟随一个 Kubernetes 和 Go 教程](https://searchitoperations.techtarget.com/tutorial/Follow-a-Kubernetes-and-Go-tutorial-in-your-home-lab?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)** 容器编排工具 Kubernetes 基于 Google 的 Go 语言。使用本 Kubernetes 和 Go 教程中的 Minikube 来管理应用程序依赖关系。

## 工具

**[参赛围棋评论家](https://blog.usejournal.com/contributing-to-go-with-go-critic-88a25e162bf6?gi=1c3f33b1292a&utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

我们已经验证了 golang/go 库，并发送了一些补丁，正在修复发现的问题。在本帖中，我们将检查建议的更改。

**[项目雅典:组件](https://medium.com/@arschles/project-athens-the-components-fc20112d61b1?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

你从雅典得到 thegithub.com/arschles/assert 包裹，而不是直接从 Github。雅典从 Github 得到它，但是它把包存储在自己的地方。

[![](../Images/7634c679372600d681fbf22b864c41dc.png)T2】](https://twitter.com/fatih/status/1019643295490871296)

**[纯 Go 小型家用互联网路由器](https://github.com/rtr7/router7?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

Router7 是一个小型家用互联网路由器的纯 Go 实现。它配备了使 fiber7 互联网连接工作所需的所有服务(DHCPv4、DHCPv6、DNS 等)。).

**[介绍散文 v2.0.0:带 NLP 走](https://medium.com/@errata.ai/introducing-prose-v2-0-0-bringing-nlp-to-go-a1f0c121e4a5?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

我们很高兴地宣布 prose 的 v2.0.0 版本，这是一个面向 Go 的自然语言处理(NLP)库。

[![](../Images/15a79fcc6ee723797a64037365cbd9d8.png)T2】](https://twitter.com/neo4j/status/1019943617744515082)

## 谈戈朗

**[going to Go:Go 之旅的垃圾收集者](https://blog.golang.org/ismmkeynote?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)**

Go 特性目标和用例迫使我们重新思考整个垃圾收集栈，并把我们带到了一个令人惊讶的地方。这篇演讲描述了我们的旅程。

[![Episode 124\. nil and AST · ](../Images/48eb3db187d74a96497e2c532c684657.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--W2aE-_CH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/revue/items/images/003/240/196/thumb/cover.png%3F1532117990) ** [第 124 集。零和 AST](http://golangshow.com/episode/2018/06-17-124-en/?utm_campaign=Revue%20newsletter&utm_medium=Newsletter&utm_source=Master%20the%20World%20of%20Golang)—[golangshow.com](http://golangshow.com/episode/2018/06-17-124-en/)**

第 124 集[恩]。零和 AST。额外收获:在@GopherConRu 采访@bradfitz 和 [@bketelsen](https://dev.to/bketelsen) 。

每周[马尔滕·贝泽默](https://medium.com/@veger)7 月 22 日[免费加入](https://www.getrevue.co/profile/golang/issues/master-the-world-of-golang-issue-9-120281?utm_campaign=Issue&utm_content=view_in_browser&utm_medium=email&utm_source=Master+the+World+of+Golang)或添加[时事通讯 rss](http://fetchrss.com/rss/5b27d6348a93f858388b4567878277344.xml)

```
Email address

First name <span>(Optional)</span>

Last name <span>(Optional)</span> 
```

Enter fullscreen mode Exit fullscreen mode