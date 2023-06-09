# 为什么没有服务器

> 原文：<https://dev.to/yos/why-serverless--3pn7>

> 在 [yos.io](https://yos.io/) 找到更多有趣的文章

[![](img/48ef9100182ad1958c14cd2e73f51aad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vi3526j1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/sggEtUC.jpg)

网络应用传统上部署在运行在物理机器上的 [**网络服务器**](https://en.wikipedia.org/wiki/Web_server) 上。

作为软件开发人员，您需要了解运行您的软件的服务器的复杂性。为了让应用程序在服务器上运行，您可能需要花费数小时来下载、编译、安装、配置和连接各种组件。您机器的操作系统需要不断升级和修补安全漏洞。

管理服务器是一项耗时的任务，通常需要经验丰富的专职系统操作人员。

[![What server maintenance can feel like -- Metropolis (1927 film)](img/afa188b5f5d18792eafd9a2c7f8258d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--elpuFFOY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/tyDSU2V.jpg)

> 软件工程的意义是什么？

与一些人的想法相反，软件工程的目标不是交付软件。软件工程师的工作是传递价值——将软件的有用性传递到用户手中。

最终，您需要服务器来交付软件。然而，花在管理服务器上的时间本可以用来开发新功能和改进应用程序。不要担心服务器，你要专注于*发货*。

当你有一个伟大的想法时，你最不想做的事情就是建立基础设施。我们如何最大限度地缩短产生影响所需的时间？

## 移向云端

在过去的几十年里，网络和平台层(操作系统和应用程序之间的技术)的改进使得云计算变得更加容易。

回到过去(20 世纪 90 年代初)，开发人员只有裸机硬件来运行他们的代码，获得新计算单元的过程可能需要几天到几个月的时间。扩展需要大量的详细规划、大量的时间，最重要的是，需要资金。转变是不可避免的。[虚拟机](https://en.wikipedia.org/wiki/Virtual_machine)和[虚拟机管理程序](https://en.wikipedia.org/wiki/Hypervisor)的发明通过虚拟化将配置新计算单元的时间缩短至几分钟。今天，[容器](https://www.docker.com/what-container)给了我们一个新的秒计算单位。

DevOps 在此期间不断发展和成熟，导致了 [**基础设施即服务**](https://en.wikipedia.org/wiki/Infrastructure_as_a_service) (IaaS)和 [**平台即服务**](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS)提供商的激增。这些第三方平台允许您将维护其代码执行环境的任务委派给有能力的人，从而将软件开发人员从服务器和部署问题中解放出来。

[![Platform-as-a-Service (PaaS) providers allows developers to develop, run, and manage applications without the complexity of building and maintaining their own infrastructure](img/8d1a91b74cfa9974896dcb079fb33659.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YoPwIwpO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/NBIGc62.png)

今天，开发人员已经不再将软件部署在客厅里的物理计算机上。您无需在每个服务器实例上手动下载和构建大量平台级技术(以后在扩展时必须重复这一过程)，而是可以在您选择的 PaaS 提供商上访问一个简单的 web 用户界面，单击几个选项，让您的应用程序自动部署到一个完全配置的集群。

当您的应用程序使用量增加时，您可以通过单击几个按钮来增加容量。当您需要额外的基础设施组件、设置部署管道或启用数据库备份时，您可以从同一个 web 界面完成这些工作。

如今，平台即服务(PaaS)和云计算的状态既方便又强大——但是我们能做得更好吗？

## 进入无服务器

云计算的下一个重大转变通常被称为“无服务器”或“功能即服务”(FaaS。)

> 请记住，短语“无服务器”并不意味着不再涉及服务器。这仅仅意味着开发者不再需要考虑那么多。
> 
> 计算资源被用作服务，而不必围绕物理容量或限制进行管理。

[![Cloud Computing Evolution](img/c87ff17d0f54ef9d9dd174abd2fd73c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X3jeTpxU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/slhgi6y.png)

无服务器是一种**软件开发方法**，旨在通过以下方式消除基础设施管理需求:

*   使用**托管计算服务(功能即服务)**执行您的代码，以及
*   利用**外部服务和 API**(第三方软件即服务产品。)

现在有大量的第三方服务:处理在线支付、交易电子邮件、用户分析、代码质量测量、内容管理、持续集成和其他次要问题的 API。在我们的日常工作中，我们还利用外部工具进行项目管理、文件共享、办公室管理等等。

[![Third-party services developers rely on for software development.](img/119cd97119f57c01dd0529cabfd9b5e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cTgxoEU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Uw5uDmB.png)

开发人员可以*专注于他们的核心价值主张*，而不是花费宝贵的资源来构建诸如基础设施和服务器维护之类的次要功能。开发人员可以将预制的部分连接在一起，并从应用程序中删除次要的复杂性，而不是从头开始构建。
通过利用第三方服务，您可以快速构建松散耦合、可伸缩且高效的架构。

## 在关闭

无服务器运动和 FaaS 平台是将基础设施问题委托给更有能力处理这些问题的公司的重要一步。无论你在 DevOps 做得有多好，亚马逊/谷歌/微软几乎肯定会做得更好。现在，您可以获得现代集装箱农场架构的所有好处，而无需倾家荡产或花费数年时间自己构建。

为什么[要去无服务器](https://www.goingserverless.co)？所以你可以一直运输。

> 在 [yos.io](https://yos.io/) 找到更多有趣的文章