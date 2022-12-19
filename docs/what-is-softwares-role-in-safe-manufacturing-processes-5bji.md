# 软件在安全生产过程中的作用是什么？

> 原文：<https://dev.to/levente/what-is-softwares-role-in-safe-manufacturing-processes-5bji>

[EaglePicher Technologies](https://www.eaglepicher.com/) 是一家领先的电池系统制造商，服务于国防、航空、航天或医疗等多个行业。
由于他们在高度监管的行业中运营，保持清晰可追溯的软件代码更改历史与遵守硬件规范和安全要求一样重要。

电气和计算机工程师助理 Kalen Brown 分享了他们如何使用[提交策略插件](https://marketplace.atlassian.com/plugins/com.midori.jira.plugin.jira-commit-policy-plugin/server/overview?utm_source=dev-community-blog&utm_medium=eaglepicher-jcp-success-story&utm_campaign=content-marketing)来管理 Subversion 库中的源代码变更。

### 告诉我们一点你在 EaglePicher 的角色吧！

我在 2015 年初从大学毕业后就开始了这里的工作，并基本上投入到了我现在从事的项目中。

我们现在正在研究一种锂离子磷酸铁(LFP)商用飞机电池，这种电池使用更安全、更坚固的化学物质。
由于磷酸铁混合物的存在，这种电池中的电池可能比普通锂离子电池安全得多，但我们仍然希望尽可能地安全，因此我们将硬件和软件结合在一起，让电池保持良好状态。

### 是什么触发了你对一个严格控制的存储库的需求？

联邦法规(CFRs)的[代码，由联邦机构创建的规则集合，表示飞机所需功能的设备、系统和装置必须设计为确保它们在任何可预见的操作条件下执行其预期功能。
美国联邦航空管理局(FAA)是监管民用航空所有方面的国家机构，该机构给出了一个可接受的合规方法示例，以证明该要求为](https://www.ecfr.gov/cgi-bin/text-idx?SID=c0d41fd915ac99598eeed4a302a6c183&mc=true&node=pt14.1.25&rgn=div5#se14.1.25_11309) [RTCA DO-178C 机载系统和设备认证中的软件考虑事项](https://en.wikipedia.org/wiki/DO-178C#Traceability)，这是当局(如 FAA、EASA 和加拿大交通部)用于监管基于软件的商业航空航天系统的框架。
它明确规定了定义机载系统和设备软件方面的预期功能的要求，以及证明这些功能的测试。

[![Boeing 787 Dreamliner aircraft uses Li-Ion batteries](img/7c5cc5d53d2b1da05e522c994ea72f77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rLSCrW8W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gvfdd0qkf3xo7sk2sruv.jpg) 
*波音 787 梦想飞机出现电池爆炸起火的严重问题*

同样重要的是要注意，这些指南不仅仅是为了让我们创建更多的文档。例如，我们需要经历的一些困难是因为 2013 年波音 787 梦想飞机上的[电池起火。](https://en.wikipedia.org/wiki/Boeing_787_Dreamliner_battery_problems)

所以我们需要能够*证明*每个需求是如何被满足的，以及被源代码的哪一部分满足的。

这就需要一些工具，让我们能够轻松地管理每个需求的工作项，并以一种主动的方式控制提交给 Subversion 存储库的内容。这些需求驱使我们使用吉拉、Subversion 以及随后的提交策略插件来帮助进行变更控制。

### 提交策略插件如何帮助开发者保持合规？

提交策略插件有助于确保过程符合性，本质上，您在 Subversion 中更改某些内容的原因必须记录在吉拉中。

它通过对照[预定义的规则](http://www.midori-global.com/products/jira-commit-policy-plugin/documentation?utm_source=dev-community-blog&utm_medium=eaglepicher-jcp-success-story&utm_campaign=content-marketing#anatomy-of-commit-policies)验证发送到 Subversion 存储库的所有提交来实现这一点，并在出现问题时提醒开发人员。
提交不符合要求的提交是不可能的，所以在代码审查时，我们可以只关注软件做了什么，让提交策略插件检查是否有正确的条件和引用。

这样，维护了简单的可追溯性，人们可以查看包含提交消息的 Subversion 修订历史并找到文档(策略让我们键入的吉拉问题密钥)。

我们还推出了(更被动的)Subversion ALM 应用程序，因此查看吉拉问题时，审核者可以找到代码中发生变化的地方，因为 Subversion commit 消息出现在吉拉问题的 Subversion 选项卡中。

## 在 Subversion 存储库中使用提交策略有什么好处？

虽然我们可以随时编写一个正则表达式来检测提交消息中是否输入了类似吉拉票证模式的内容，但是提交策略的 [JQL 条件](http://www.midori-global.com/products/jira-commit-policy-plugin/documentation?utm_source=dev-community-blog&utm_medium=eaglepicher-jcp-success-story&utm_campaign=content-marketing#issue-in-jql-result-condition)确保了所提到的票证确实存在。

举个例子:你可以把 DEMO-72 放到一个提交消息中，它看起来像一个吉拉问题密钥。然而，提交策略可以告诉我们，虽然 DEMO-72 与所需的模式相匹配，但它并不存在，因为我们只完成了 DEMO-5。
或者，它确实存在，但被分配给了另一个吉拉项目中的其他人。
它可以避免你创建不正确或损坏的链接。

有时，它还可以避免您简单地键入错误的问题密钥。
比如说，我输入了 DEMO-893，但是我输入了 DEMO-839，提交策略拒绝了我的提交，说提到的问题不在我应该提交的状态。
因此，除了让我们的开发人员跟踪合规的代码变更，它还有点像拼写检查器。

与大多数标准一样，DO-178C 的目标是将一个过程正式化，在这个过程中，所做的事情易于审查，并遵循常识性的尽职调查。然而，这导致 99%的工作是生成文档(远远超出这里讨论的范围), 1%的工作是编辑源代码，所以我们对文档应用的规则与对源代码应用的规则相同。
令人欣慰的是，Subversion 这样的工具有助于自动记录一些跟踪事情所需的文档，Commit Policy Plugin 提醒我们记录我们正在做的事情，或者我们需要在改变事情之前让其他人看看。

### 对于那些正在犹豫是否向他们的存储库引入提交策略的人，你有什么话要说？

当有一个过程需要遵循时，那么这就是要得到的工具*。*

让一个专门的人看着每一个转移或记录为什么没有遵循流程，这样可以节省时间。如果人们遵循手边的过程，那么他们应该*永远不会*遇到提交策略，它将是无缝的。

您可以将其范围缩小到特定的文件夹，如“tags”文件夹，或者对整个存储库应用一个规则，即提交消息不能为空，甚至不能使用任何吉拉功能，如 JQL。

最困难的方面是向其他人传达你希望他们遵循的过程，所以有了[提交策略](https://marketplace.atlassian.com/plugins/com.midori.jira.plugin.jira-commit-policy-plugin/server/overview?utm_source=dev-community-blog&utm_medium=eaglepicher-jcp-success-story&utm_campaign=content-marketing)这可能是一次火的考验，你的辛勤工作不会被烧毁。