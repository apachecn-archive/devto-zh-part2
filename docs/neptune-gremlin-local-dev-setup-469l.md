# Neptune (Gremlin)本地开发设置

> 原文：<https://dev.to/designfrontier/neptune-gremlin-local-dev-setup-469l>

我现在正在玩一些图形数据库，这是一个有趣的项目，我以后可能会写。当我第一次开始使用 Neo4j 时，它有一个优秀的本地开发故事，一个可爱的基于 web 的 UI，用于可视化您的数据和 CYPHER，这是一个非常容易掌握的图形语言。

所有对它有利的伟大标志。

然而，由于项目的其余部分将是一系列的 lambdas 和 api 网关，并且将在 AWS 中托管，我对 Neptune 产生了好奇。哦，因为我对扩展和维护 graph DB 基础设施的了解比我希望的要少。使用 Neptune，一个托管的 graph DB，让我暂时摆脱了许多基础设施知识。

但似乎没有好的地方发展故事。

然后我想起，在图形数据库的世界中，最重要的事情是为目标数据库使用的查询语言找到匹配。

Neptune 使用 Gremlin 作为它的主要查询语言，而 Gremlin 恰好是 Apache TinkerPop 的主要语言。所以我运行了一个基于 docker 的 TinkerPop 实例。这至少让我有了数据库服务器。让我告诉你，我在 gremlin 头上撞了很长时间，比我想承认的要长。你可能比我做得更好，但是要花时间阅读文档和介绍。查询概念可能与您过去处理的不同。gremlin 比 CYPHER 感觉更陌生。

因此，我想要的第二件事是一种容易地将我正在写入图表的数据可视化的方法。当您试图弄清楚查询语言是如何工作的，以便能够看到您的查询是否因为没有任何内容而没有返回任何内容，或者您是否写错了，这是非常方便的。

在寻找用什么的时候，我偶然发现了 https://github.com/bricaud/graphexp，它为基于数据库的精灵提供了一个简单的用户界面。不是世界上最漂亮的，但它很实用，能让你很快看到我正在寻找的一切。

用 docker-compose 将所有这些连接起来非常简单。我的 graphexp 的 docker 文件和 docker-compose for the whole thing[都在 github](https://github.com/designfrontier/gremlin-local) 上，便于重用。

一旦我在本地运行了它，就开始需要通读 TinkerPop 的文档了。有很多。但它相当不错。我也开始阅读[凯文·劳伦斯的《实用小精灵](http://kelvinlawrence.net/book/Gremlin-Graph-Guide.pdf)，它非常有帮助。

希望这些能帮助你使用基于 gremlin 的图形数据库！