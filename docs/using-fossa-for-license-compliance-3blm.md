# 使用 FOSSA 实现许可证合规性

> 原文：<https://dev.to/pullrequest/using-fossa-for-license-compliance-3blm>

在一轮或退出的勤奋过程中，技术领导者经常会感到惊讶:问题清单很长，令人生畏，似乎是为了减缓过程。我见过有一种尽职调查要求让回合脱轨并不像看起来那么难管理...显影工具[窝](https://www.fossa.io)

项目通常充斥着来自许多来源的代码。从通过包管理器导入到从堆栈溢出复制和粘贴(这是一个非常危险的实践，因为建立一个所有权链几乎是不可能的)，开发人员以各种方式添加依赖关系。

对于收购者或风险资本家来说，这是有问题的。举个例子:脸书对 React 的最初许可包括一个合理的知识产权分配，理论上可以阻止一家公司起诉脸书任何与专利有关的事情(他们后来将它重新许可给了麻省理工学院)。对于一家初创公司来说，这是一个公平的交易:在法律范围内与脸书打交道的可能性很低，而使用 React 获得的速度很高。

甚至像某些 GPL 版本许可的包这样的项目对公司的合规团队来说也意味着不可接受的风险。

从历史上看，这意味着所有人都要检查代码库，在无法确定代码作者的地方进行重构，或者花大价钱雇佣外部咨询公司来做这件事。在我最后一次退出(以及我们在这里的 A 轮融资)中，我发现了一个摩擦更少的更好的方法。

##### 步骤 1)报名 FOSSA

GitHub、Bitbucket 和 GitLab 集成是可用的，如果您在其他地方托管代码，还可以手动上传项目的归档。确保选择您是分发代码还是提供 web 服务，因为 FOSSA 会建议一套合理的规则来检查您的项目。

[![Step 1 Sign Up for FOSSA](img/689064c43f5976043b58cfa592e716e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C5H--APD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yfa1ali4457oimspw7he.png)

##### 步骤 2)通过 FOSSA 运行你的代码

等待检查员运行；成功的运行将被固定到您手动上载的项目的分支或版本。

[![Step 2 Run Your Code Through FOSSA](img/79e728924fb1629e20db22c9bea0938e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pnMZzRXY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r50vcrh38wiubabl5oru.png)

##### 步骤 3)生成报告

将出现一个兼容、不兼容和遵守措施项目的列表。大多数律师事务所想要这些页面的 pdf 格式；他们是有益的打印格式，并使一个很好的组合文件时，完成。

[![Generate a Report](img/79e728924fb1629e20db22c9bea0938e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pnMZzRXY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r50vcrh38wiubabl5oru.png)

这就是全部了。虽然这不会完全取代对开发团队不良行为的严格监控(例如，复制和粘贴整个堆栈溢出答案)，但它会在尽职调查期间为您节省大量时间。

在 [PullRequest](https://www.pullrequest.com/?utm_medium=referral&utm_source=dev.to) 时，我们建议将此作为您工作流程的一部分。我们将结果标记附加到我们的每个存储库，这样我们就知道我们是否引入了与我们的目标策略不兼容的东西。

这是我的一个开源项目的许可证徽章的例子(一个后台搜索服务的 PHP 包装器)

[![FOSSA Status](img/7ff4300ac5bab3126a25339b00e711b7.png)T2】](https://app.fossa.io/projects/git%2Bgithub.com%2Flyal%2Fcheckr?ref=badge_shield)

*这篇文章最初发布在 [PullRequest 的博客](https://www.pullrequest.com/blog/?utm_medium=referral&utm_source=dev.to)上。让您的代码接受专业评审员的评审:[今天就报名](https://www.pullrequest.com/?utm_medium=referral&utm_source=dev.to)参加 PullRequest*