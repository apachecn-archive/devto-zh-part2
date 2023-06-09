# 凤凰计划 Pt1

> 原文：<https://dev.to/bennysbanter/the-phoenix-project-pt1-3n4p>

最近，DevOps 的一位朋友(也是导师)推荐我阅读 Gene Kim、Kevin Behr 和 George Spafford 的《凤凰计划》,这是我在成为 DevOps/SRE 领域工程师的旅程中学习课程的一部分。

让我现在就推出剧透潮流。这本书绝对是 IT 行业任何人的必读之作，无论你是哪个部门或专业。现在，继续。

如果你以前没看过，凤凰计划是关于一个叫比尔·帕尔默的行动技术员的旅程。比尔陷入困境，被迫接管一家制造公司的整个 IT 部门，该公司濒临破产，由于软件解决方案无法与竞争对手竞争而被外包。Bill 发现的是开发和运营团队之间的内部战争，这给公司留下了延迟和不完整的发布(我们以前都经历过)。这个乱七八糟的家伙开始寻求解决这个臭名昭著的问题，这个问题已经困扰 IT 行业几十年了。在接下来的几个月里，在一些伟大人物的指导下，Bill 学会了 DevOps 的原则，并将公司完全转变为一台自动化的智能工作机器。

我从凤凰计划中学到了什么实践经验？

## 它的工作完全像一个制造工厂的车间:

在车间里，原料从左边进入，成品从右边离开。这些材料在组装时要经过一系列的工作中心。我们称之为在制品或“WIP”

特定的工作中心有时会成为制约因素，也就是说，在所述工作中心完成特定任务所需的时间比导致该任务的 WIP 流程要长。在 IT 界，我们称之为技术债务；在现有问题得到解决之前出现的新请求、问题和错误。最终，这导致了不可估量的技术债务。为了解决这个问题，必须控制 WIP 流向约束条件，以免开始技术债务的积累。

## 观想给出概观:

能够可视化 WIP 对于维持其流程至关重要。看板是一个很好的工具。

基本看板由三列组成:“要做的”、“正在做的”和“完成的”这些列包含任务卡。所有未完成的和必需的任务卡都在“待办事项”栏中。然后将这些卡片进行优先排序，并以较小的数量移入“执行”栏，以免造成限制。只有完成后，它们才能被移到“完成”栏。

在所有现有的卡片完成之前，不应该将卡片放入“正在做”栏。这一过程确保了工作的持续流动，并确保在任何给定时间只有优先任务处于显微镜下。

## 每天十次部署:

DevOps 成功的关键是持续改进。虽然在过去的 15 年中，开发人员已经变得敏捷和快速地响应新的请求，但是操作却落后了。开发人员希望他们的代码能够快速而有规律地发布，而运营人员不想对服务器做任何改动，以免破坏服务器。开发人员想要速度，运营人员想要稳定性。为了解决这个难题，运营需要变得敏捷。我们通过几个工具和概念来实现这一点。

主要的概念被称为代码或“基础架构编码”在云计算的世界中，我们可以编写脚本或代码(像编程语言一样)来根据需要启动和自动化云基础设施。这消除了运营部门手动配置监控基础架构的需要。

我们的第二个首选工具集是持续测试和持续集成。通过使用 infracoding，我们可以创建开发和 QA 环境来精确地克隆我们的生产环境。然后，我们使用工具创建测试和条件，新代码必须通过这些测试和条件才能进入下一阶段。当应用新代码时，使整个过程自动化会带来令人难以置信的速度，并使我们能够在出现问题时回滚到以前的版本。

这在很大程度上为客户消除了停机时间和可见的问题。虽然 DevOps 工作流还有许多其他部分，但这些关键组件是让 Ops 变得敏捷并实现速度和稳定性的驱动力。

## 结束语:

DevOps 的这些方面涵盖了实现 DevOps 工作流的实际日常应用。在未来的一篇文章中，我计划回顾 DevOps 的“三种方式”,深入探讨 DevOps 运动的哲学层面，以及这些实践和工具如何适应更大的公司图景。

现在，我从 Phoenix 项目中学到的这些经验教训可以应用到你今天的工作流程中。