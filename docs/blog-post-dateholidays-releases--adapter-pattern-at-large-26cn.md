# Blog post: Date::Holidays 发布——适配器模式广泛存在

> 原文：<https://dev.to/jonasbn/blog-post-dateholidays-releases--adapter-pattern-at-large-26cn>

自[日](https://dev.to/jonasbn/dateholidays-110-released-181o)假期【1.10 版帖子发布以来；

–[1.11](https://github.com/jonasbn/perl-date-holidays/releases/tag/1.11)改进了对 Date::Holidays::SK

–[1.12](https://github.com/jonasbn/perl-date-holidays/releases/tag/1.12)改进了对 Date::Holidays::US federal as US

–[1.13](https://github.com/jonasbn/perl-date-holidays/releases/tag/1.13)支持 Date::Holidays::CA_ES，via Date::Holidays::ES

–[1.14](https://github.com/jonasbn/perl-date-holidays/releases/tag/1.14)标记 Date::Holidays::UK 和 Date::Holidays::UK::England 不支持，使用 Date::Holidays::England

我还在准备更多的版本。

所有这些工作开始时主要是试图弄清问题列表的底部，当我在不同的角落和改编时，新的问题确实会出现，但这完全没问题，我可能永远不会弄清问题列表的底部，但至少 Date::Holidays 发行版会改善并稳定下来。

这项工作也是由视角的变化引起的，我最初的动机是创建一种方法来整合和使用所有不同的 Date::Holidays::*发行版，而不必调整所有发行版的不同接口。

我刚刚发现我的文档缺少一个关于动机的部分，描述为什么约会::假期——问题列表中的另一件事。

新的观点是，许多发行版并没有真正被更新(这很遗憾)，但是我并没有为相关的发行版创建补丁，而是在 Date::Holidays 中调整适配器以实现可能缺少的功能，而不是将补丁发送给各个发行版的作者——我可能会在以后这样做，但是因为这需要大量的工作，所以在大多数情况下，反过来会更快更容易。不幸的是，也有可能原作者没有反应，我的补丁将永远不会发布，所以这个策略可以被描述为一个“安全胜于遗憾”的实现。

关于更快和更容易，我正在创建小任务，这些任务可以在通勤时完成。我一直受到里卡多·森纳斯的启发，我认为他比我在电脑前写的通勤代码还要多。这可能是*轻微的*夸张，如果我的描述不成比例，里卡多必须纠正我，同时，没有什么像一个好的程序员的神话-里卡多确实是一个多产的 CPAN 贡献者，因此他不需要神话。

无论如何——我不是一个经常通勤的人，我将主要在夏天骑自行车，所以这可能不会以这种频率继续下去，但我确实喜欢微小的任务，我会尽可能把它们挤出来。

整个视角的改变(以及我对罗伯特·c·马丁(Robert C. Martin)的“ [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) ”的阅读)吸引了我很多注意力，当我处理所有实际问题时，我也在考虑更大的图景。

Date::Holidays 一直是并将永远是一个附带项目，偶尔有人联系我使用它或正在尝试它，这让我非常高兴，但我没有欺骗自己-我不认为 Date::Holidays 有很多观众。我确实有新实现的 PRs，这很棒，我会继续推动更多的集成和调整，但这并不能改变用户群有限的事实。

那么为什么我一直在 Date::Holidays 上编码呢？

嗯，它非常有教育意义。

当我开始的时候，这是一个在[适配器模式](https://en.wikipedia.org/wiki/Adapter_pattern)中的练习，它可能仍然是，但是事情已经改变了。现在我正在阅读上面提到的“干净的代码”,我正试图采纳和学习书中的一些原则。

这让我想到了约会::假期的新路线图，它正在慢慢成形。

–采用一些“干净代码”原则

–排除“nocheck”标志(这是原则之一)

–排除在隐式国家列表上工作的一般功能，这根本不属于该类(这可能是原则之一)

–评估可能采用的格式参数 from Date::Holidays::* distributions

–评估可能实现的数据本地化 from Date::Holidays::* distributions

所以现在会有很多小版本来改进实际的适配器，在某个时候，我会考虑做一个大版本，把 Date::Holidays 提升到一个新的水平，有很多干净的代码，希望我会在这个过程中学到很多。