# 最好的自动化部署工具是…符合您需求的工具

> 原文：<https://dev.to/geshan/the-best-automated-deployment-tool-is-the-one-that-fits-your-needs-3o8>

从开发环境中获取完整的特性或错误修复代码到 web 服务器的过程就是 web 软件部署。我们一直在以各种方式部署代码，十多年前，我们习惯于使用文件传输协议(FTP)上传文件。我不认为将文件从开发机器复制到 web 服务器是自动化部署的一种形式，这在当时是必要的。

如果你仍然像 2015 年 22%的尼泊尔开发者那样使用 FTP，你需要继续前进。在这篇文章中，我将简要介绍一些部署工具，并尝试分析它们的优缺点。尽管如此，正如标题所示，最好的自动化部署工具是最符合您需求的工具。

[![The best automated deployment tool is… the one that fits your needs](img/20195c4927bd267bb9e4f80b73dc3ed6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OnnZEv4Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AW0vvTrhYpesTt8yv.png)

## 什么是自动化部署？

Web 应用程序部署需要完成一些预定义的步骤，以便将软件更改从开发环境传送到试运行/生产环境。这样做是为了让客户/用户可以使用工程团队开发的新功能。如果您的部署过程的部分或大部分是手动的，那么它就不是自动化部署。例如，如果您通过 ssh 连接到您的服务器，并执行 git pull，这不能被视为自动化部署过程。

> 自动化部署是一种部署形式，其中将代码从开发环境运送到登台/生产环境的定义步骤是一个单步过程，并且是完全或部分自动化的。

在我看来，自动化部署对于每个应用程序来说都是一种与语言/框架无关的需求，因为它节省了大量时间。这可能是迈向有用的 DevOps 文化的第一步。开发和系统管理员/devops 团队可以一起工作来实现自动化。它还将打开进一步自动化的大门，比如连续交付，例如您的测试通过了 CI 服务，代码可以自动部署到您的临时服务器上。

## 自动化部署特征

它应该具有以下特征:

*   它可以被一个动作触发，比如命令行上的一个命令，它就能完成任务。

*   这些步骤将是预定义的、可重复的和可预测的。

*   自始至终很少或没有人为干预。

*   它应该显示部署进度，因为它发生了，更好的反馈

*   它应该是原子性的，这意味着要么完成所有步骤，要么什么都不会发生。

## 好有特色

自动化部署工具的一些好特性是

*   它应该能够将相同的代码部署到多个服务器上

*   每个部署都应该从 git 这样的版本控制系统(VCS)的给定分支/标签/提交中完成

*   它应该以电子邮件/聊天消息的形式触发通知

*   每个人都应该能够查看部署了哪个分支/标签

*   当一个部署正在进行时，它应该停止其他部署来启动

*   上一次部署的回滚应该简单而快速。

## 可用的免费工具

让我们来看看如果您开始脱离手动方法进行自动化部署，可以使用哪些免费工具:

*   用红宝石写的
*   [Fabric](http://www.fabfile.org/) -用 Python 写的
*   米娜(用红宝石写的)
*   [罗基特](http://rocketeer.autopergamene.eu/) -用 PHP 编写
*   [部署者](http://deployer.org/)——用 PHP 编写

## 那么我该选哪个呢？

我不能从上述选择中给你一个明确的赢家，这将完全取决于你的需求。我可以简单描述一下我用过的两个工具。我用过[卡皮斯特拉诺](http://capistranorb.com/)和[面料](http://www.fabfile.org/)。让我们看看它们有什么不同。

## 卡皮斯特拉诺

用 ruby 编写的 Capistrano 作为自动化部署工具已经存在很多年了，它非常稳定，支持很多语言和框架，从 Symfony 到 NodeJs 等等。你可以从这个[播客](https://changelog.com/110/)和官方[文件](https://github.com/capistrano/capistrano/blob/master/README.md)中获得更多信息。

> Capistrano 的好处是它已经有了一个关于如何部署应用程序的已定义的[流程](http://capistranorb.com/documentation/getting-started/flow/)。

如果你能直观地理解流程。你可以学习如何制作/编辑食谱，并在完成任务后组织你的任务。Capistrano 的另一个好处是，它保留了发布版本，并在当前版本就绪时进行符号链接切换。回滚速度很快，因为它只是一个到代码的旧的成功部署版本的符号链接切换。

## 织物

用 python 编写的 Fabric 也是自动化部署领域的老玩家了。强烈推荐阅读 it's [docs](http://docs.fabfile.org/en/1.10/) 。您还可以获得一些应用程序的部署脚本。主要区别是 Fabric 更像是一个远程命令运行器。

> 与 Capistrano 相比，您可以随心所欲地构建部署流程。您可以编写部署命令，并按照您想要的顺序运行它。

它让您可以自由地构建您的部署过程。您可以为部署过程选择所需的任务序列。

## 其他工具

一般来说，其他工具是基于 Capistrano 风格的部署，有人说它们比 Capistrano 更快，因为它们批处理 ssh 命令并运行一次。基本思想不变。我个人不能认可任何其他工具，因为我已经自己使用过它们。

## 接下来呢

如果您已经在进行自动化部署，您可以考虑让它变得更简单，比如从 Hipchat 或 Slack 这样的聊天界面进行部署。它被称为 [ChatOps](http://blog.flowdock.com/2014/11/11/chatops-devops-with-hubot/) ，人们可以在其中指示机器人部署应用程序。如果你不想走聊天这条路，你甚至可以构建一个 web 界面来触发部署，就像 Zendesk 的 [Samson](https://developer.zendesk.com/blog/introducing-samson-a-web-interface-for-deployments) 一样。

如果您运行您的测试，您甚至可以探索连续交付。

2018 年，我会建议看一看 [Docker](https://docker.com) 。

> Docker、 [Kubernetes](https://kubernetes.io) 和 [Helm](https://helm.sh/) 的组合可以让你的部署过程非常顺利。

不过提前警告一下，Docker，K8s 和 Helm 的设置可能会花你一些时间。花费的时间和资源会在未来带来更高的回报，尤其是对于更大的团队。

## 结论

您将决定选择适合您的语言/框架、应用程序和团队需求的正确的部署工具。试着选择一个能很好地完成工作，并且能长时间使用而不会出现重大问题的工具。

> 如果你想部署一个大的应用程序，想要稳定性和结构，就选择像 Capistrano 这样更安全的选项。如果您刚刚开始为一个较小的应用程序进行自动化部署，并且希望使用您的定制流程进行自动化部署，那么请使用 Fabric，因为它具有灵活性。

选择合适的工具并开始自动化部署。对 FTP 说拜拜，然后在服务器上做 ssh。我们开始吧！

* * *

最初发表于[geshan.com.np](http://geshan.com.np/blog/2015/08/the-best-automated-deployment-tool-the-one-that-fits-your-needs/)。