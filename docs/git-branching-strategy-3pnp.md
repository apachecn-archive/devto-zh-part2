# Git 分支策略

> 原文：<https://dev.to/subbramanil/git-branching-strategy-3pnp>

# Git 分支策略

我们使用“git”作为我们的版本控制，使用 Bitbucket 作为我们的托管提供者。

最近，我被要求担任团队 bitbucket 的管理员，因为负责管理的人要离开公司了。我在做一些分支的清理工作，我发现有很多分支在合并后没有被删除。此外，它给了我一个机会来回顾我们团队使用的分支策略。

我想分享我们使用的策略，希望它能帮助一些人。我还希望获得任何有助于我们改进战略的反馈或建议。

我们使用两种分支策略。

## 功能开发

[![Feature Development branching strategy](img/dc2424b37f34e3a79b7c65d8ff910b6b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0U9qjcGX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/my-blogresources/Feature_development_branching_strategy.png)

## Bug 修复

[![Bug Fixing branching strategy](img/203dcbda4a00bc67261433bece494b93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EvSsjCdN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/my-blogresources/Bug_fix_branching_strategy.png)

**注意:**为了简单起见，我只添加了合并/释放时的主要提交点。

### 主控分支

*   这是存储库的主要分支，我们从这里发布到商店或发布服务器。
*   一般规则:
    *   访问受到限制。
    *   只能通过拉请求提交更改。不允许直接提交。
    *   商店/生产发布服务器上应始终有最新的发布版本。
    *   不应该有未发布的代码(已经提交但尚未发布)。

### 培养分支

*   这是库的主要“开发”分支，我们从这里发布 alpha/beta 版本。这个分支是基于创造的“主”分支。它跟踪当前的发展。
*   一般规则:
    *   访问受到限制。
    *   正常情况下不允许直接提交。如果出现某种情况，可以在整个团队的同意下直接提交。
    *   可以通过拉请求提交更改。
    *   在特性开发期间，不应该从用户分支发出直接开发的拉请求。从功能分支向“开发”发出“拉”请求。(用户 _ 分支->特征 _ 分支->开发)
    *   所有团队成员都应该批准拉取请求。
    *   应该总是拥有最新的 alpha/beta 发布版本。它不应该有未发布的代码(已经提交但尚未发布)。
    *   用户/功能分支的基本分支。

### 特征分支

*   这是正在开发的特性的主要分支。这个分支是基于“发展”分支的创造。当我们开发一个新功能时，我们会在“开发”分支的基础上创建一个功能分支“开发 _ 功能”。在功能完成之前，“develop_feature”分支将作为开发的基础分支。
*   一般规则:
    *   访问受到限制。
    *   只要更改很小(例如，发布版本更改或签名密钥更改)，或者只有一个人在处理功能分支，就允许直接提交。
    *   可以通过拉请求提交更改。
    *   拉取请求应该由所有团队成员批准(如果实习生的工作安排不合适，实习生是可选的)。
    *   应始终在完成功能后合并回“开发”。它不应该有在特性完成后不与“开发”合并的提交。
    *   一旦特征分支被合并到“开发”分支并被适当标记，它们就有资格被删除。一旦特征分支发布到“生产”中，它们就可以被删除。
    *   如果需要的话，可以从特性分支中发布 alpha/beta 版本。

### 用户分支

*   用户分支是从针对单个开发人员的“开发”或“功能”分支创建的。它通常遵循以下格式，“开发用户名”或“开发功能用户”取决于基本分支。
*   一般规则:
    *   同一开发人员可以有多个分支，例如，开发用户名，开发用户名 06 05 2017 或开发功能用户名，开发功能用户名 2
    *   另一个用户不允许直接提交到一个用户分支。
    *   可以通过拉请求提交更改。
    *   提取请求应由分支机构的所有者批准。
    *   发布不应该从用户分支进行，尽管如果情况出现，可以向 QA 进行中间测试发布。
    *   一旦特性被合并到开发中并发布到 alpha/beta，基于“特性”分支的用户分支可以被删除。

### Bug 修复/升级/测试分支

*   这些分支是临时分支，创建这些分支是为了修复 bug 或解决升级问题，或者执行一些小的测试。分支可以基于“开发”或“功能”分支。它应该适当地遵循命名约定，以表明分支的目的。

    `e.g, develop_JIRA-1511, develop_ESC-1008, test_develop_fake_service.`

*   这种命名约定的原因是 Atlassian 产品是相互连接的，如果分支被适当地命名，它们可以自动地将这些分支链接到适当的 bug。

*   一般规则:

    *   允许直接提交。
    *   不止一个分支可以用于错误修复、升级或测试。为了区分，开发者可以使用数字、用户名等。
    *   可以通过拉请求提交更改。
    *   提交拉取请求时，建议选择‘合并后关闭分支’。
    *   合并后可以手动删除这些分支。
    *   过时的分支可以删除。由开发人员负责确保通过拉请求将更改提交到“开发”或“开发 _ 功能”分支。
    *   决不能从这些类型的分支中发布。

这个策略对我们来说一直很有效，我们是一个由 5 个开发人员组成的小团队，负责 2 个产品。到目前为止，我们使用这种策略还没有发现任何问题。但是我们总是乐于改进，所以请随意提出任何建议。