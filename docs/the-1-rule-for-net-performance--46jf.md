# 的首要规则。净绩效

> 原文：<https://dev.to/elemarjr/the-1-rule-for-net-performance--46jf>

[![](../Images/8d23abd80fb1bc1a35a77e252fd0bbca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8ZFQtWbC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/chuttersnap-381972-unsplash-300x200.jpg)

如果你问我一个提高应用程序性能的技巧，那就是:

**将您的物品设计为在`gen #0`收集或根本不收集。**

自然，遵循这个建议要求您[至少了解垃圾收集的基本工作原理](http://If%20you%20ask%20me%20one%20tip%20to%20improve%20the%20performance%20of%20your%20applications,%20it%20would%20be:%20Design%20your%20objects%20to%20be%20collected%20on%20gen%20#0%20or%20not%20at%20all.%20Naturally,%20following%20this%20recommendation%20demands%20you%20to%20know%20how%20the%20garbage%20collection%20works.%20But,%20if%20you%20are%20interested%20in%20improving%20the%20performance%20of%20your%20applications,%20this%20is%20necessary%20at%20all.%20In%20fact,%20the%20garbage%20collector%20was%20explicitly%20designed%20to%20be%20very%20efficient%20performing%20gen%20#0.%20Garbage%20collector%20gets%20more%20expensive%20in%20each%20generation.%20Even%20with%20background%20processing%20for%20gen%20#2,%20there%20is%20still%20a%20very%20high%20CPU%20cost%20to%20pay.%20Consider%20that%20you%20should%20avoid%20gen%20#1%20collections%20too.%20In%20most%20scenarios,%20gen%20#1%20objects%20are%20directly%20promoted%20to%20gen%20#2.%20Ideally,%20every%20object%20you%20allocate%20goes%20out%20of%20scope%20by%20the%20time%20next%20gen%20#0%20comes%20around.%20You%20can%20measure%20how%20long%20is%20that%20interval%20and%20compare%20it%20to%20the%20duration%20that%20data%20is%20alive%20in%20your%20application.%20Time%20to%20action%20Following%20this%20recommendation%20requires%20a%20shift%20in%20your%20mindset.%20It%20will%20inform%20nearly%20every%20aspect%20of%20your%20application.)。但是，如果您对提高应用程序的性能感兴趣，这是非常必要的。

事实上，垃圾收集器被明确设计为非常高效地执行 gen #0。

垃圾收集器每一代都变得更加昂贵。即使有 gen #2 的后台处理，仍然有很高的 CPU 成本要支付。考虑到您也应该避免 gen #1 集合。在大多数情况下，gen #1 对象被直接提升到 gen #2。

理想情况下，在下一个 gen #0 出现时，你分配的每个对象都超出了作用域。您可以使用诸如 [PerfView](https://github.com/Microsoft/perfview) 之类的工具来测量该时间间隔，并将其与数据在您的应用程序中的持续时间进行比较(顺便说一下，花点时间学习如何使用 perf view——这是一个非常棒的工具)。

## 行动时间

遵循这个建议需要你转变心态。它将告知你申请的几乎每一个方面(我强烈推荐你这个 [Ayende 关于这个话题的演讲](https://www.youtube.com/watch?v=eRjWYjEf8n4&feature=youtu.be))。

以下是编写代码时要记住的一些准则:

*   池长寿命和大对象——考虑[遵循罗斯林团队](http://www.elemarjr.com/en/2017/06/roslyn-performance-lessons-1-the-objectpool-and-the-pooledstringbuilder/)采用的相同模式。
*   尽可能避免复制数据。使用类作为 [ArraySegment](https://msdn.microsoft.com/pt-br/library/1hsbd92d(v=vs.110).aspx) ，现在，使用新的 Span < T >(请花些时间和[阅读这本由 Stephen Toub 写的 MSDN 杂志](https://msdn.microsoft.com/en-us/magazine/mt814808.aspx))。
*   减少对象之间的引用——这将降低 GC 的成本。此外，它使得预测对象的生存期变得更加容易。
*   注意[终结器](https://ayende.com/blog/181665-A/the-cost-of-finalizers)的成本。
*   使用弱引用进行缓存

这还不够吗？在接下来的几周，我将分享关于你如何遵循这些建议来提高的深入信息。NET 应用程序性能。登录我的联系人列表，当新内容到达时，您会收到通知。

最后但同样重要的是，记住[性能是](https://blog.codinghorror.com/performance-is-a-feature/)的一个特性。如果您的应用程序的性能不是那么好，那么您很可能会错过交付商业价值的机会。

封面图片: [chuttersnap](https://unsplash.com/@chuttersnap?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)

帖子的第一条规则是。净表现最早出现在【ElemarJR.com】的[上](http://www.elemarjr.com/en)。