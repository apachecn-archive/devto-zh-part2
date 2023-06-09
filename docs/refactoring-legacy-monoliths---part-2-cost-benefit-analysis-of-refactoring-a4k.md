# 重构遗留系统——第 2 部分:重构的成本效益分析

> 原文：<https://dev.to/jamesmh/refactoring-legacy-monoliths---part-2-cost-benefit-analysis-of-refactoring-a4k>

你如何说服管理层投入时间和金钱来重构你的遗留系统？

让管理层相信重构的好处是值得的，对许多人来说可能是一个停止点。你的工作是提供重构遗留整体的成本收益分析，并让你的团队相信这是有意义的。

顺便说一句，这是我之前关于这个话题的帖子的续篇，在那里我讨论了面对这个话题的出发点:

*   可以测试的代码——首先
*   和...进行测试
*   业务逻辑与你的表现逻辑相分离
*   停止浪费时间构建已经在稳定/经过测试的第三方库中可用的代码
*   等等。

让我们看看如何克服这个过程中的下一个障碍。

# 路障

在这一点上，你和你的开发团队都在船上，但你(或你的高级开发人员)没有像这样的重大决策的最后发言权。所以，你需要说服管理层，大规模的重构是必要的。

但是有一个问题:**你的公司对*制作软件*不感兴趣，他们想*赚钱*和*取悦他们的客户*** 。这是您的非开发经理或公司所有者(即使他是开发人员)可能会想到的:

> 花费几周甚至几个月去改变一堆代码(比如重构)并不会真正影响我们的用户！它甚至不会改变客户看到的东西以及他们使用我们产品的方式。相反，我们应该构建新的功能来为我们的客户提供更多的价值。毕竟，给用户更多的价值=我们口袋里更多的钱。

所以，你有一个问题。你**知道**你拥有的软件难以维护，难以扩展，无法测试，不可移植。但是，这对于那些需要决定是否付钱给你去做必要工作的人来说毫无意义。

你如何克服这一点？

# 你需要把开发者的话题转换成金钱的话题

答案很简单——但只需要做一点工作。你需要将你的每一项“开发者利益”转化为他们将如何为公司省钱。换句话说，一份正式的报告和对重构给你公司带来的好处的分析。

> 将所有技术收益转换为$$$$

你必须自己研究每个领域。根据您的代码和系统所在的位置，最终报告会因产品而异。

以下是您的报告的一个小样本，包括:

* * *

#### 重构的成本收益分析*此处插入系统*由*你*

*   在设计阶段捕获 bug(通过使用测试驱动开发)比部署后修复节省了公司 100 多倍的成本

*   在开发期间捕获 bug(不使用严格的测试驱动开发，而是实现基于代码的测试)可以为公司节省 15 倍于部署后修复的成本

*   实施代码审查可以为公司节省 30 个小时来修复遗漏的软件缺陷，以及每小时投入的维护时间

*   拥有一个重构的代码库意味着未来的特性将会被更快地开发(更少的时间浪费在试图找出我们的遗留设计并扩展它上)

*   拥有一个行业标准化的架构使新开发人员能够更快地“启动并运行”

*   使用现代工业标准化技术意味着:

    *   总体发展速度加快
    *   该公司对潜在雇员更有吸引力
    *   增强的安全性——不易出现安全漏洞
*   如果我们将我们的业务逻辑从我们的表现逻辑中分离出来，这意味着现在我们可以*实际上*使用当前系统的所有特性构建以下*:*

    *   公共 API
    *   移动 API
    *   移动应用程序
    *   桌面应用程序
    *   等等。

* * *

# 最终提示

将你的研究收集到“直截了当”的要点中，强调这将如何省钱。然后，呈现给任何需要听的人。

确保你包括每一点能为公司节省多少时间和/或金钱的数字和确切数字。这是关键。“开发者利益”对于希望自己的业务盈利的人来说没有任何意义。金钱和时间才是最重要的。不要忘记包括报告(就像例子中一样)。

# 最后一只兔子的踪迹

首席/高级开发人员需要做的每个决定都应该这样做。例如，通过引入免费咖啡、更多休假时间、远程选项等各种福利，让开发人员“更快乐”。可以表现为省钱。怎么会？它增加了员工保留率和公司对潜在雇员的吸引力。而且，你可以把这些好处列为公司网站上的营销噱头，向所有人展示你的公司有多棒——这可能会吸引潜在客户。

# 谢谢！

我希望这是有帮助的，给你一些有用的提示！

# 保持联系

别忘了在 [twitter](https://twitter.com/jamesmh_dev) 或 [LinkedIn](https://www.linkedin.com/in/jamesmhickey/) 上联系我！

# 导航您的软件开发生涯

一封电子邮件简讯，我将在其中回答订阅者的问题，并就以下主题提供建议:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？

听起来有趣吗？加入社区吧！