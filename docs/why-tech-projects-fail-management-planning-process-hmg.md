# 为什么科技项目会失败:管理、计划、过程

> 原文：<https://dev.to/robdwaller/why-tech-projects-fail-management-planning-process-hmg>

科技项目总是失败，或者产出低于预期。例如，产品可以工作，但是代码很糟糕。作为开发人员，我们都参与过这样的项目，或者不得不整理它们，这可能会令人沮丧。因此，了解技术项目失败的原因非常重要，这样我们就可以减少失败的发生。并有希望使开发成为一种更愉快的活动和工作。

作为开发人员，当我们讨论项目失败时，我们通常指的是代码质量。低质量代码有四个特征:

*   写得不好
*   没有文档
*   婴儿车
*   无法满足业务需求

许多开发人员认为代码质量与原则和测试有关。这是合乎逻辑的，因为未能遵循原则或编写测试的开发人员会产生写得很差、错误百出的代码。但是代码原则和测试虽然重要，但不会导致项目失败或直接降低代码质量。它们是症状而不是根本原因。他们的缺席凸显了项目的失败，但并没有解释失败的原因。

项目失败有三个原因:

## MPP

*   管理
*   规划
*   过程

## 管理

管理和管理者是项目失败的基础。项目中可能出错的一切都源于管理。

如上所述，糟糕的代码之所以会产生，是因为开发人员没有编写测试或遵循代码原则。但是开发人员不遵循代码原则或编写测试的原因是由于经验和/或时间。

是管理者定义了经验和时间。经理是雇佣开发人员的人，如果他们只雇佣没有经验的开发人员，事情会很快出错。没有经验的开发人员通常能够使产品工作，但是他们并不总是意识到原则、测试和文档的重要性。通常他们会挣扎于推后，告诉管理层他们错了。代码质量只会在没有经验的环境中受损，这最终会导致项目失败。

没有经验的开发人员没有错，我很长一段时间都是这样。虽然没有经验的开发人员需要支持，必须分配合适的工作。不应该给他们太多的责任，他们必须得到指导。最好是由能够解释测试、原则、文档和关注细节的人来完成。

时间是管理者努力解决的另一个问题，是复杂性的结果。项目越复杂，需要的时间就越多。经理们经常不能理解项目的复杂性，因此设定了不切实际或不明智的时间表。这施加了太大的压力，承受压力的开发人员经常会跳过测试和原则。这将总是导致糟糕的代码和错误。

经理的行为与代码质量和项目失败有着紧密的联系。如果管理者做出错误的决策，代码质量就会下降，项目就会失败。如果管理者想要避免项目失败，他们需要为他们的项目雇佣合适的团队。他们还需要了解项目的复杂性，以确定现实的时间表，这需要规划。

## 规划

规划有四个目标:

*   了解项目复杂性
*   突出已知的未知
*   最小化未知的未知
*   定义优先级。

许多组织未能充分计划，有些甚至拒绝计划。他们看不到它的价值，或者认为他们的项目很简单，有可能“继续工作”。在我的职业生涯中，我只在一家做了适当规划的机构工作过。这是非同寻常的，因为我从事专业编码已经超过十年了。这也意味着我的职业生涯充满了失败和低质量的代码。

计划是必不可少的，每一个项目和任务，不管多小，都需要计划。作为一名经理和开发人员，假设一个项目或任务比看起来更复杂是明智的。大多数任务，一旦你考虑测试，编码，文档，审查和部署，至少需要两到三天才能完成。如果你以小时为时间线，你可能没有正确考虑复杂性。

那么规划是什么样子的呢？它可以采取多种形式，但正如前面提到的，它的主要目的是理解复杂性。

这可以通过和客户坐在一起写一些用户故事来实现。定义他们希望他们的用户实现什么，为什么。或者，你可以聘请业务分析师和 UX 专家来了解和记录客户的业务和用户需求。注意，UX 专家是研究用户行为的人，而不是仅仅绘制网站地图和线框的人。

还有[行为驱动开发](https://en.m.wikipedia.org/wiki/Behavior-driven_development)。[西亚兰·麦纽提](https://twitter.com/ciaranmcnulty)已经在 PHP 社区推动 [BDD 原则](https://youtu.be/83GbyDpJDI4)很多年了。在他最近在 PHP London 的演讲中，他强调了示例映射的好处，它旨在通过编写需求、规则和示例来理解项目的复杂性。

一个示例要求可以是:

```
Shop A has a user loyalty scheme.

For each dollar spent by a user the user receives one loyalty point. 
```

Enter fullscreen mode Exit fullscreen mode

从表面上看，这个要求似乎很简单。一美元相当于一个忠诚度积分。但有些问题需要回答，有些规则需要定义，比如，税收是什么？

这就是一个例子可以帮助你的地方:

```
User 1 purchases Product A.

Product A costs $10 and there is 20% tax.

Product A's total cost is $12.

User 1 receives 10 loyalty points. 
```

Enter fullscreen mode Exit fullscreen mode

通过一个例子，忠诚度系统的复杂性更加清晰。用户只获得产品成本的忠诚度积分，而不是税收。现在想象一个大项目有上百个例子。突然之间，项目变得更容易理解，需要做什么变得更清晰，并且更容易测试。未知的未知数也大大减少了。

如果你还没有读过[行为驱动开发](https://en.m.wikipedia.org/wiki/Behavior-driven_development)，我建议你读一读。即使你不能使用它，潜在的原则将帮助你成为一个更好的开发者和/或管理者。

项目优先级也必须在编码开始前明确。如果数据输入是系统最重要的方面，不要从构建通知系统开始。

优先级必须基于证据，而不是突发奇想。首席执行官或经理认为某件事很重要，并不意味着它就是重要的。优先级应该基于用户反馈、用户研究或财务状况。如果你因为复杂的结账流程而失去了 20%的订单，那么在你实施 CEO 的品牌重塑计划之前，先解决这个问题。

优先级也会随着时间和新信息而变化。因此，必须对其进行跟踪，并将其反馈到每个功能的规划中。如果你没有一个基于证据的清晰的优先列表，那么你将无法交付你需要的东西。

规划是项目成功的关键。开发人员需要绝对清楚他们需要生产什么。如果没有清晰的计划，没有明确的优先级和需求，他们就无法满足所有的业务需求。没有计划，代码质量也会受到影响。如果开发人员不得不随着新需求或优先级的发现而不断修改他们的代码，那么“黑客攻击”就会开始。结果，测试和原则将被抛弃，质量将下降，项目将开始失败。

## 流程

过程是一个好的技术项目的最后一个方面。它就像蛋糕上的樱桃。当讨论过程时，我们必须接受代码只是过程的一个方面，而且不是最重要的。如果你的过程看起来像“写代码”、“部署代码”，那就大错特错了。

技术流程有三个目标:

*   确保质量
*   最小化 bug
*   避免延误

一个简单的技术流程可能是这样的:

*   冲刺规划
*   密码
*   代码审查
*   错误修正
*   质量保证
*   错误修正
*   部署
*   健全性检查

您可以添加更多的步骤，比如文档和自动化测试。但重点是“代码”只是一个多步骤过程的一部分。最重要的部分是测试和审查。无论开发人员多么有经验或者计划多么好，你都必须假设会有错误。所以必须有捕捉和修复 bug 的步骤。

良好流程的另一个方面是沟通。你的团队之间必须有明确的沟通方式。对他们来说，能够快速交流问题或障碍尤为重要。对我来说，这是一个五到十分钟的每日站立，对其他人来说，这可能是一个松弛的通道。需要快速暴露问题，因为这将最小化延迟，因为它允许重新分配资源和修改优先级。

最后，团队合作至关重要。开发人员永远不应该独自在孤岛中工作。每一项工作都应该由多个开发人员共同完成。代码审查将涵盖其中的一些内容，但重要的是开发人员一起计划和讨论他们的工作。这样有助于更快解决问题。这也将减少压力，因为没有一个开发人员会觉得要对一项工作单独负责。

如果你还没有一个清晰定义的过程，一个好的起点是 Joel 测试。它是由 [Joel Spolsky](https://twitter.com/spolsky) 在 2000 年写的，它定义了一个好的开发团队和过程的 12 个方面。你可能不同意所有的观点，但其中很多都是有价值的建议，在近 20 年后仍然适用。用它来评估你的团队今天在哪里，然后让它来指导你的进步。取得满分是很难的，但是如果你只在两到三个方面有所进步，你就会感受到好处。

没有过程代码，质量会受到影响。开发人员将独立工作，产生不同质量的代码，大量的错误将会进入生产。这种连锁反应可能是巨大的。它们甚至可能导致整个项目的失败，因此实现一个结构良好的过程是至关重要的。

## 预算神话

科技界的一些人会说，不可能把 MPP 搞对。他们通常会争辩说没有足够的预算，因此没有时间进行适当的管理、规划和流程。

> “我们只需要继续工作……”

这是软件开发中最大的误区之一。使用它的人也显示了他们对“技术行业”的无知。

事实是，每一个技术项目都有一个相对于团队的“完全成本”。完全交付一个项目需要一定数量的开发人员日。完全交付意味着所有的业务需求都得到了满足，项目没有 bug。

最终，总会满足全部成本。唯一的问题是发行前或发行后的全部成本是否得到满足，以及由谁来满足。发布到产品中的 bug 必须被修复，所有者、客户或供应商需要支付这些费用。

发布前可以走捷径，但发布后成本将开始螺旋式上升，并导致额外的成本。发布后修复 bug 的成本要高得多。通常是因为它们没有预算，可能需要结构性的改变，减慢其他工作，并且需要增强的测试。缺少文档意味着开发人员需要更长的时间来理解现有的代码。这将降低速度，增加修复错误和系统扩展的成本。未完全满足的业务需求可能会降低其他团队的效率，从而推高业务其他部分的成本。这最后一点可能会对企业产生重大的未被追踪的财务影响。

例如，我为一个客户设计了一个数据仓库和可视化系统。我预测一个由三名开发人员和一名兼职项目经理组成的团队需要大约六个月的时间来交付第一版。客户对这个报价不满意，把我的设计给了一家公司，这家公司说他们会在八周内完成这个项目。

项目开始六个月后，它仍然没有完全交付。同样为了节省时间，相关开发人员决定将可视化工具直接连接到 MongoDB。在我的计划中，MongoDB 是数据转换管道的一部分。一个临时的数据存储，数据可视化工具从来没有打算连接到它。这种快捷方式节省了时间，但这意味着数据分析师没有标准化的、结构良好的数据集可在可视化工具中使用。仓库对他们来说几乎毫无用处，他们无法完成自己的工作，于是开始寻找替代品。

在这种情况下，客户决定采用便宜的报价，而不是现实的报价。结果，项目超出了预期，导致数据团队的业务效率低下。这个项目旨在帮助的团队。最终，这个项目的全部成本将得到满足，希望由供应商，我最初的报价可能会大大超过。该项目甚至有可能被搁置，造成巨大的资金浪费。

事实是，如果一个项目花费 10 万美元来完成，你将支付 10 万美元来完成它。如果你试图偷工减料，只花 50，000 美元，随着时间的推移，你很可能会花费超过 100，000 美元。减少预算的唯一方法是减少需求的数量，直到工作可以完全满足您的预算。

管理、规划和流程对于任何技术项目的良好运行都至关重要。没有它们，代码质量会受到影响，项目也会失败。它们还与项目的财务状况密切相关，如果它们不正确或被忽视，成本将会急剧上升。作为开发人员，我们需要关注 MPP，并在担心代码之前确保它是正确的。没有好的管理、计划和过程，就不可能写出好的代码并生产出所有开发人员都想做的好产品。