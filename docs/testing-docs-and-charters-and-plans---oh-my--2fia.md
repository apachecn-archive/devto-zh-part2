# 测试文件和章程和计划-哦，我的天！

> 原文：<https://dev.to/mafriend/testing-docs-and-charters-and-plans---oh-my--2fia>

我最近开始了一份新工作，我有幸为几个产品建立了一个测试基础。这些产品中的每一个都有不同的开发和测试需求、不同的部署周期和不同的工作流。但是不管有什么不同，我认为有一些特定于测试的原则是任何项目都应该共享的。测试应该是可访问的(任何人都可以测试！)，民主化了(测试人人有责！)，并且高度文档化(等等，你测试的是什么？).

在这篇文章中，我将捕捉我对测试文档的想法，以及我如何利用它来做好事而不是坏事。这种类型的文档旨在记录单/问题或功能实现级别，以获取测试范围、测试配置和矩阵、测试计划(包括验收标准、自动化需求、边缘案例和要考虑的风险)、测试说明以及测试期间参考的其他重要文档。

### 检测范围:

把这想象成我们想要测试的的*。范围也可以被认为是测试的边界，并且应该帮助通知测试执行者我们需要在哪里集中我们的测试工作，以及什么可能被认为超出了范围。例如:如果我们知道一个特性只接触产品的一部分，我们就不需要用同样的强度测试整个产品。测试范围可能还包括一个测试章程，它几乎可以被认为是一个使命陈述。*

示例:分析 MapMaker 的视图菜单功能并报告潜在风险区域。

关于测试章程(和基于会话的测试)的更多信息，请看一下 Jonathan Bach 的文章[“基于会话的测试管理”](http://www.satisfice.com/articles/sbtm.pdf)，来自*软件测试和质量工程*。

### 测试配置:

请将此视为我们想要测试的*的技术延续。也许有一个特定的操作系统和浏览器配置，我们希望确保我们一定会成功。如果有许多不同的组合，这可能更容易使用测试矩阵可视化。在执行测试时，测试人员应该标记出哪些配置通过、失败，以及测试过程中可能出现的任何警告。举个例子:当用户试图使用最新版本的 Chrome 登录时，某个 Windows 版本开始间歇性地抛出错误。警告应该在空闲时提出，或者放在标签中，以便开发人员或测试人员进一步调查。*

测试配置还可以包括不同的用户、权限级别、移动设备和操作系统以及环境。

### 测试计划:

以下是给定测试计划中应该包括的考虑事项。

#### 验收标准和边缘情况:

这最好由整个团队来定义，并且可以以给定的/何时/然后的格式来完成。验收标准不仅应该涵盖快乐的路径(或者产品应该如何表现)，还应该涵盖我们可能想到的任何边缘情况，并列出代码库中可能需要根据即将到来的工作进行更改的区域。通过作为一个团队清楚地定义验收标准，我们能够对实现代码变更所需的工作范围有全面的了解，并对需要交付多少开发和测试工作有更好的了解。在这里展示任何边缘案例也给团队一个发现任何可能来自开发工作的潜在问题的开端。

从测试人员的角度来看，提出验收标准并与团队一起对边缘案例进行头脑风暴，提供了一个从整体上审视产品的机会，并允许更明智和外科手术式的测试执行。

#### 自动化注释:

这应该包括我们当前的覆盖范围是什么样子，需要添加什么自动化(无论是在单元、集成还是 UI 级别)，以及任何自动化想要的。自动化需求可能小到几个集成或单元测试。自动化需求可能包括覆盖几个案例和不同配置的一整套测试。无论哪种方式，都应该在测试计划和 Github 问题中捕捉需求，这些问题可以在 sprint 中处理，或者在未来工作的待办事项中优先处理。

#### 总体风险:

在文档中封装风险是一件棘手而复杂的事情。风险可以涵盖任何东西，从自动化测试覆盖中的缺口到之前在代码库的那个部分工作时发现的已知问题。在这里识别风险的目的是帮助测试人员了解自信地说他们已经“完成”测试一个问题所需的测试水平，并且在测试计划中提出特殊的测试需求。

### 测试注意事项:

从手工和自动化的角度来看，这都可以看作是测试正在执行的*结果*。包括执行测试时做的笔记，自动化结果(来自每夜的构建，手动触发的运行，等等)。)、在测试过程中发现的错误、任何被认为超出此问题范围的问题(例如:功能改进)。考试笔记很重要。对于测试执行者来说，获取实际测试的内容以及在测试过程中发现/发现的内容是至关重要的，这样团队的任何成员都可以重温测试文档，并对执行的内容有一个全面的了解。

### 其他重要文件参考:

测试文档中应列出任何其他相关文档，以便于参考。添加这个文档还将使没有参与初始对话的其他人能够了解变更的全部范围，并且能够自信地执行测试。相关文档的例子可能是设计规范、技术规范(正在进行的附加 API 调用及其包含的内容)、相关/依赖 Github 问题、sprint 规划文档以及任何初始社交或营销材料。

#### 外卖

测试文档就是你所说的那样。我强烈建议你和你的团队谈谈，讨论一下你总体上认为哪些文档是有价值的，可扩展的，并且能被所有团队成员广泛理解的。不要忘记考虑产品本身和其中的特性！上面关于测试计划的提议可能对某些项目有效，但是您可能需要做一些调整，以最适合您和您的团队正在构建的东西。也许思维导图会更好！也许创建一个共享生活电子表格最适合你！也许让四只树懒串在一起，从一棵天蚕树上悬挂和取回测试便利贴是你的绝对难题！也许最好的测试文档是以上所有的组合。看你的了！