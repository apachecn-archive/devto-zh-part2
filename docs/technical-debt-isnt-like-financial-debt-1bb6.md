# 技术债务不同于金融债务

> 原文：<https://dev.to/thaiwood/technical-debt-isnt-like-financial-debt-1bb6>

我最近与一些组织的一些领导人交谈过，我听到了一种共同的哀叹。在讨论他们的架构或过程的某个地方，他们向下看，摇了一下头，说了一些类似“嗯，我们有技术债务”的话，然后才考虑什么解决方案对他们可用。

在引入新特性之前，考虑软件和过程中可能需要的返工是一个好主意，但是像金融债务这样的技术债务的比喻会导致一些问题。

把技术债务想成实际的，金融债务表面上可能是有意义的。他们都是“债务”，对吗？

问题是，当把你想做的技术改进当成“债务”时，有很多错误的假设会被带走

为什么会这样？

金融债务的一些特征:

1.  所有的债务都还清了
2.  债务有利率，因此很容易衡量，回报也是可以预测的。(因为其利率是债务协议的一部分)
3.  只要你的信用评分还在，你就可以借钱
4.  你忽视的债务会增加
5.  债务可以用来买东西
6.  你可以宣布破产

你看到这里的问题了吗？带着这个比喻经常意味着认同这些想法，其中许多并不正确，因为它适用于技术问题、解决方案和返工。

让我们来解决这个比喻带来的最大谬误:债务必须偿还。

这根本不是真的。并不是每一个稍微不完美的设计决策或代码都需要被重新访问并返回到一个完美的状态或以其他方式返工。也许这段代码不经常被读取并且运行良好。也许它很少改变。

相反的情况也可能是真的，一些小事情会导致你的团队在上面花费大量的时间，无论是通过运行、修补还是理解。

将所有可能需要返工的代码库或过程称为“技术债务”也可以鼓励人们从事第 3 点；继续借钱，这是一种“记在我账上”的心态，一旦你有了债务，就更难意识到增加更多债务所带来的问题。

这些特性中有一些确实是重叠的，你可以忽略代码或者做出设计决策，这将导致以后的返工以换取出货。这有点像用你的“债务”来“购买”东西，但是价格不是很清楚，这使得这可能是一个很难很好评估的交易。

你也有能力宣布破产。你可以只说“我们已经结束了，我们正在做一个完整的重写”，这在某种程度上让你摆脱了以前的债务，但也给你带来了建立新东西的负担。你不是在归零，你实际上是在变负。

我是在说不要“偿还”任何“技术债务”还是说你永远不能重写？不，当然不是。我是说，把它想成债务，想成你“不得不”或“应该”偿还的东西，而不去进一步思考它，更有目的性是次优的，这可以让你不会错过要点。

这并不意味着你不能使用这个比喻，或者需要把它从你的词汇中删除。相反，更直接地考虑和概念化这个问题是有好处的，作为一个负担，而不是一个经常很少衡量，定义不清的池子，你可以继续把东西倒进里面。

*   "由于这种[变化，事情，等等]，需要多少人的干预？"
    *   这有助于过滤掉技术上完美的东西，例如旧的技术框架等..什么会真正导致某人不得不做出反应、修复或其他反应。
*   “这是否会阻止我们在其他地方进行改进？”
*   "我关心的是具体的东西还是这种过早的优化？"
*   “许多人会因此在这里被绊倒和/或沉没很多时间吗？”

你同意吗，你觉得金融隐喻有用吗？你是为了还债而还债吗？你在平衡运输和返工方面有什么经验？[给我发邮件](//mailto:Thai@ThaiWood.IO)