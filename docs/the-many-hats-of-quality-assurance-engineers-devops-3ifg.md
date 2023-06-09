# 质量保证工程师的众多头衔:Devops

> 原文：<https://dev.to/angelariggs/the-many-hats-of-quality-assurance-engineers-devops-3ifg>

*本文最初发表在我的博客上，[http://Angela Riggs . github . io](http://angelariggs.github.io/blog/many-hats-quality-assurance-engineers-devops)。*

* * *

注意:这是关于我作为质量保证工程师的不同角色的系列文章的第二篇。你可以看看这里的第一个帖子[，在那里我谈到了戴上我的测试帽！](http://angelariggs.github.io/blog/many-hats-quality-assurance-engineers-tester)

那么，我的 DevOps 角色长什么样？先从定义开始吧！还有[很多](https://newrelic.com/devops/what-is-devops) [更长的](https://www.atlassian.com/devops) [解说](https://jvns.ca/blog/2016/10/16/whats-devops/)，但这是我的 TL；DR version: DevOps 是一组实践和过程，用于促进不同角色之间的协作，以便创建用于创建、部署和维护代码的可持续方法。(这里有一个小问题:从技术上讲，这不是一个角色，尽管这个术语通常是这样使用的，例如 DevOps Engineer)。

在被聘为质量保证工程师后不久，我戴上了 DevOps 的帽子。让我们的项目和团队建立持续集成/持续部署(CI / CD)是我们工程总监 Dylan 的首要任务。迪伦“休息”了几天，花时间埋头在 [Bamboo](https://www.metaltoad.com/blog/grokking-bamboo-cicd-beginners) 上设置服务器，这是 Atlassian 为 CI / CD 开发的产品。一旦设置完成，我们有一个群集日，我们几个人聚在一起，配置我们当前的所有 repos，以便通过 Bamboo 进行构建和部署。

除了作为初始设置的一部分，我还在 Bamboo 中创建了用于设置和维护我们的项目计划的文档。UI 和过程往往有些不直观，文档允许其他人相对容易地成功建立新项目。我也是将我们的 Bamboo 版本升级到主要版本的团队的一员(包括期待已久的通过代码配置的特性！).有了 CI / CD，我们就有了每次提交的构建检查，所以问题有可能在合并或部署步骤之前就被发现。它还允许我们在部署过程中出现问题时更容易地进行故障排除，并在需要时回滚失败的部署。

Metal Toad 在我开始担任 QA 工程师的时候实施了敏捷开发团队，为团队建立标准开发工作流是我最先做出的改进之一。在我开始之前，质量保证步骤没有正式的工作流程。测试是在每个项目的基础上进行的——通常集中在引人注目的项目上。代码审查可能每周进行一次，使用一个工具，允许我们回顾每个项目的提交历史。这是乏味的，并提供了一个非常肤浅的审查机会。这些审查也从来没有导致代码改进——我们没有经常使用拉式请求(PRs)来合并工作，所以这些会议实际上只是为了讨论。在团队内部，在 sprints 中工作，没有办法判断什么时候可以进行 QA 检查，或者进行代码审查。我们将拉请求添加到我们的流程中，但是工程师们通过 Slack 消息传递来交流评审准备情况。这是杂乱无章的，但这也意味着试图通过 QA 和代码审查遵循最佳实践是有压力的。

解决方案实际上非常简单——为我们在吉拉的 sprint 板创建一个工作流(这实际上非常耗时，但是一旦你习惯了吉拉的工作方式，就不会太复杂了)。我为每个工单状态设置了一个栏——待办事项、进行中、QA、代码审查、PO 审查、完成。我在我们的团队中领导了关于如何日常使用这个工作流的讨论，我们建立了一个关于何时在列之间移动票证以及谁负责每个列的流程。我们一致认为，吉拉的评论是我们的真理之源——面对面的交谈、闲聊或电子邮件都必须提炼到标签上，以确保一致的共同理解。我们还将 GitHub 集成到了吉拉- Git commits 和 PR titles 中，包括吉拉票证编号，因此 commits、PRs 和 deployment 都反映在每张票证上。该工作流程允许团队的每个成员轻松查看工作状态，并了解他们在完成工作中的职责。

最近，我帮助领导了我们向集中式静态代码分析的转移。我与我们的一名移动工程师一起研究了各种工具，并创建了一个决策矩阵。我们考虑了成本、运行环境、行内注释、语言支持和文档。我们倾向于通过 GitHub 运行分析，以减少在 Bamboo 服务器上安装工具的工作量。我们也强烈倾向于在线评论——也就是说，直接在 PR 上分析反馈，而不是被迫登录到一个单独的 UI。语言支持的差异可以忽略不计，除了一个工具不支持 CSS / SASS 分析。技术文档总是时好时坏，但是我们看到的大多数工具都有足够好的文档来满足我们的需求。在我们研究的所有工具中， [Codacy](https://www.codacy.com/product) 提供了各种类别的最佳选择。在这篇文章中，我们已经有了大约 20 个当前的项目，这些项目设置了静态代码分析、基本的内部文档以供使用和反馈，以及在我们的工作流程中的另一个检查，以坚持我们代码中的最佳实践。

促进协作和沟通的实践和流程。 DevOps。我实现的所有这些工作流和工具都考虑到了这一点——减少开销和增加理解；尽可能简单地创建和维护我们的代码；建立透明的问责制和责任制。这些都是我们将不断回顾和反思的事情，并有可能重复——但现在我们有了一个坚实的基础。

***您是如何开始在贵公司实施 DevOps 实践的？你学到了什么，或者接下来你想尝试什么？在评论中分享你的经验吧！*T3】**