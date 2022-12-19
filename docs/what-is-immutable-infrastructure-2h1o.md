# 什么是不可变的基础设施？

> 原文：<https://dev.to/digitalocean/what-is-immutable-infrastructure-2h1o>

由[黑兹尔·维尔多](https://dev.to/hazelnut)

### 简介

在传统的可变服务器基础设施中，服务器在适当的位置不断更新和修改。使用这种基础设施的工程师和管理员可以通过 SSH 访问他们的服务器，手动升级或降级软件包，逐个服务器地调整配置文件，以及将新代码直接部署到现有服务器上。换句话说，这些服务器是可变的；它们可以在创建后进行更改。由可变服务器组成的基础设施本身可以被称为可变的、传统的或(轻蔑地)手工的。

不可变的基础设施是另一种基础设施范例，其中服务器在部署后不会被修改。如果需要以任何方式更新、修复或修改某些内容，则可以提供从通用映像构建的新服务器，并进行适当的更改，以替换旧服务器。经过验证后，它们将投入使用，旧的将退役。

不可变基础设施的好处包括基础设施中更高的一致性和可靠性，以及更简单、更可预测的部署过程。它减轻或完全防止了易变基础设施中常见的问题，如配置漂移和雪花服务器。然而，有效地使用它通常包括全面的部署自动化、云计算环境中的快速服务器供应，以及用于处理日志等有状态或短暂数据的解决方案。

本文的其余部分将:

*   解释可变基础设施和不可变基础设施在概念和实践上的区别
*   描述使用不可变的基础设施的优点，并把复杂性放在上下文中
*   给出一个不可变基础设施的实现细节和必要组件的高级概述

## 可变基础设施和不可变基础设施的区别

可变基础设施和不可变基础设施之间最根本的区别在于它们的中心策略:前者的组件被设计成在部署后可以更改；后者的组件被设计为保持不变并最终被替换。本教程主要关注那些作为服务器的组件，但是也有其他方法来实现不可变的基础设施，比如容器，它们应用了相同的高级概念。

更深入地说，基于服务器的可变和不可变基础设施之间存在实际和概念上的差异。

从概念上讲，这两种基础设施在对待服务器的方式上有很大的不同(例如，创建、维护、更新、销毁)。这通常用“宠物与牛”的类比来说明。

实际上，可变基础设施是一种更古老的基础设施范式，早于核心技术，如虚拟化和云计算，使不可变基础设施成为可能和实用。了解这段历史有助于了解两者之间的概念差异，以及在现代基础设施中使用其中一种的含义。

接下来的两节将更详细地讨论这些差异。

### 实用差异:拥抱云

在虚拟化和云计算变得可能和广泛可用之前，服务器基础设施以物理服务器为中心。创建这些物理服务器既昂贵又耗时；初始设置可能需要几天或几周时间，因为订购新硬件、配置机器，然后将其安装在 [colo](https://en.wikipedia.org/wiki/Colocation_centre) 或类似位置需要很长时间。

可变基础设施源于此。因为更换服务器的成本非常高，所以最实际的做法是尽可能长时间地使用已经运行的服务器，尽可能减少停机时间。这意味着常规部署和更新需要进行大量适当的更改，但出现问题时也需要进行临时修复、调整和修补。频繁手动更改的后果是服务器变得难以复制，使每台服务器都成为整个基础架构中一个独特而脆弱的组件。

[虚拟化和按需/云计算](https://www.digitalocean.com/community/tutorials/an-introduction-to-cloud-hosting?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)的出现代表了服务器架构的一个转折点。虚拟服务器更便宜，即使是大规模的，而且它们可以在几分钟内创建和销毁，而不是几天或几周。这使得新的部署工作流和服务器管理技术首次成为可能，比如使用[配置管理](https://www.digitalocean.com/community/tutorials/an-introduction-to-configuration-management?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)或[云 API](https://www.digitalocean.com/community/tutorials/how-to-use-the-digitalocean-api-v2?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)快速、有计划地自动供应新服务器。创建新的虚拟服务器的速度和低成本使得不变性原则变得可行。

传统的可变基础设施最初是在物理服务器的使用决定了其管理的可能性时发展起来的，并随着技术的进步而不断发展。在部署之后修改服务器的范例在现代基础设施中仍然很常见。相比之下，不可变的基础设施从一开始就被设计为依赖基于虚拟化的技术来快速供应架构组件，如云计算的虚拟服务器。

### 概念差异:宠物 vs 牛，雪花 vs 凤凰

云计算推进的基本概念变化是服务器可以被认为是一次性的。考虑丢弃和替换物理服务器是不切实际的，但是使用虚拟服务器，这样做不仅是可能的，而且简单高效。

传统可变基础设施中的服务器是不可替代的、唯一的系统，必须一直保持运行。这样，他们就像宠物一样:独一无二，无与伦比，需要人工照料。失去一个可能是毁灭性的。另一方面，不可变基础设施中的服务器是一次性的，很容易用自动化工具复制或扩展。从这个角度来说，他们就像牛:牛群中的一员，没有哪个个体是独一无二或不可或缺的。

引用 Randy Bias 的话，他首先将宠物和牛的类比应用于云计算:

> 在旧的做事方式中，我们像对待宠物一样对待我们的服务器，例如邮件服务器 Bob。如果鲍勃倒下了，那就全靠他了。首席执行官收不到电子邮件，这是世界末日。在新的方式中，服务器被编号，就像牛群中的牛一样。比如 www001 到 www100。当一台服务器出现故障时，它会在生产线上被拆除、拍摄和更换。

另一个类似的方式来说明服务器处理方式之间的差异的含义是雪花服务器和凤凰服务器的概念。

[雪花服务器](https://martinfowler.com/bliki/SnowflakeServer.html)类似宠物。它们是手动管理的服务器，经常更新和调整，形成了独特的环境。[凤凰服务器](https://martinfowler.com/bliki/PhoenixServer.html)类似牛。它们总是从零开始构建，并且易于通过自动化过程重新创建(或“从灰烬中重生”)。

不可变的基础设施几乎完全由牛或凤凰服务器组成，而可变的基础设施允许一些(或许多)宠物或雪花服务器。下一节将讨论这两者的含义。

## 不可变基础设施的优势

为了理解不可变基础设施的优点，有必要将可变基础设施的缺点联系起来。

可变基础设施中的服务器可能会遭受配置漂移，即未记录的临时更改导致服务器的配置彼此之间以及与经过审查、批准和最初部署的配置之间的差异越来越大。这些越来越像雪花一样的服务器很难复制和替换，使得扩展和从问题中恢复变得困难。甚至复制问题来调试它们也变得很有挑战性，因为很难创建与生产环境相匹配的试运行环境。

经过多次手动修改后，服务器不同配置的重要性或必要性变得不明确，因此更新或更改任何配置都可能会产生意想不到的副作用。即使在最好的情况下，对现有系统进行更改也不能保证有效，这意味着依赖于这样做的部署有失败或使服务器进入未知状态的风险。

考虑到这一点，使用不可变基础设施的主要好处是部署的简单性、可靠性和一致性，所有这些最终都会最小化或消除许多常见的棘手问题和故障点。

#### 已知良好的服务器状态和较少的部署失败

不可变基础架构中的所有部署都是通过基于经验证且受版本控制的映像调配新服务器来执行的。因此，这些部署不依赖于服务器以前的状态，因此不会因此而失败，或者只是部分完成。

在配置新服务器时，可以在投入使用之前对其进行测试，从而将实际部署过程简化为一次更新，使新服务器可用，就像更新负载平衡器一样。换句话说，部署变成了[原子](https://en.wikipedia.org/wiki/Linearizability):要么成功完成，要么什么都不改变。

这使得部署更加可靠，也确保了基础架构中每台服务器的状态总是已知的。此外，这一过程使得实施[蓝绿色部署](https://www.digitalocean.com/community/tutorials/how-to-use-blue-green-deployments-to-release-software-safely?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)或[滚动发布](https://en.wikipedia.org/wiki/Rolling_release)变得容易，这意味着没有停机时间。

#### 无配置漂移或雪花服务器

不可变基础架构中的所有配置更改都是通过使用文档将更新的映像登记到版本控制中，并使用自动化的统一部署流程来部署具有该映像的替换服务器来实现的。Shell 对服务器的访问有时是完全受限的。

这通过消除雪花服务器和配置漂移的风险，防止了复杂或难以重现的设置。这也防止了某些人需要修改不太了解的生产服务器的情况，这种情况存在很高的出错风险，并导致停机或意外行为。

#### 一致的暂存环境和轻松的水平扩展

因为所有服务器都使用相同的创建过程，所以不存在部署边缘情况。这通过使复制生产环境变得简单来防止混乱或不一致的暂存环境，并且还通过无缝地允许您向基础架构添加更多相同的服务器来简化[水平扩展](https://blog.digitalocean.com/horizontally-scaling-php-applications/?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)。

#### 简单的回滚和恢复过程

使用版本控制来保存映像历史也有助于处理生产问题。用于部署新映像的相同流程也可用于回滚到旧版本，从而在处理停机时增加额外的恢复能力并减少恢复时间。

## 不可变基础设施实施细节

不可变基础设施在实现细节上有一些要求和细微差别，特别是与传统的可变基础设施相比。

通过简单地坚持不变性的关键原则，实现独立于任何自动化、工具或软件设计原则的不可变基础设施在技术上是可能的。但是，为了大规模实用，强烈建议使用以下组件(大致按优先顺序排列):

*   **云计算环境**中的服务器，或者另一个虚拟化环境([比如容器](https://www.youtube.com/watch?v=S3gYxEVz_b8)，尽管这改变了下面的一些其他需求)。这里的关键是从自定义映像中快速配置隔离的实例，以及通过 API 或类似工具自动管理创建和销毁。

*   **整个部署流程的完全自动化**，理想情况下包括创建后映像验证。设置这种自动化大大增加了实施这种基础架构的前期成本，但这是一次性成本，很快就会摊销掉。

*   一个 **[面向服务的架构](https://en.wikipedia.org/wiki/Service-oriented_architecture)** ，把你的基础设施分成模块化的、逻辑上离散的单元，通过网络进行通信。这使您可以充分利用云计算的产品，这些产品[类似于面向服务的](https://en.wikipedia.org/wiki/Cloud_computing#Service_models)(例如 IaaS、PaaS)。

*   一个 **[无状态](https://en.wikipedia.org/wiki/Service_statelessness_principle)，易变的应用层**，其中包括您的不可变服务器。这里的任何东西都可以在任何时候被快速破坏和重建(易失性)，而不会丢失任何数据(无状态)。

*   一个**持久数据层**，它包括:

    *   **集中式日志记录**提供有关服务器部署的更多详细信息，如通过版本或 Git commit SHA 进行映像识别。因为服务器在这个基础设施中是可任意处理的(并且经常被丢弃),所以在外部存储日志和度量允许调试，即使在 shell 访问受到限制时或者在服务器被破坏之后。
    *   **外部数据存储**用于数据库和任何其他有状态或短暂的数据，如[DBA as/云数据库](https://en.wikipedia.org/wiki/Cloud_database)和[对象或块存储](https://www.digitalocean.com/community/tutorials/object-storage-vs-block-storage-services?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)(云提供或自我管理)。当服务器不稳定时，您不能依赖本地存储，因此您需要将数据存储在其他地方。
*   **工程和运营团队的奉献精神**协作并致力于该方法。尽管最终产品很简单，但在一个不可变的基础设施中有许多移动的部分，没有人会知道所有的内容。此外，在这个基础设施中工作的某些方面可能是新的或者超出了人们的舒适区，比如调试或者在没有 shell 访问的情况下执行一次性任务。

有许多不同的方法来实现这些组件。选择哪一个很大程度上取决于个人偏好和熟悉程度，以及你希望自己构建多少基础设施，而不是依赖付费服务。

[CI/CD 工具](https://www.digitalocean.com/community/tutorials/ci-cd-tools-comparison-jenkins-gitlab-ci-buildbot-drone-and-concourse?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand)是部署管道自动化的良好开端； [Compose](https://www.compose.com/) 是 DBaaS 解决方案的一个选项； [rsyslog](https://www.digitalocean.com/community/tutorials/how-to-centralize-logs-with-rsyslog-logstash-and-elasticsearch-on-ubuntu-14-04?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand) 和 [ELK](https://www.digitalocean.com/community/tutorial_series/centralized-logging-with-elk-stack-elasticsearch-logstash-and-kibana-on-ubuntu-14-04?utm_source=devto&utm_medium=display&utm_campaign=Devto_2018_Brand) 是集中测井的热门选择；网飞的混乱猴子(Chaos Monkey)会随机杀死您生产环境中的服务器，这是对您最终设置的真正考验。

## 结论

本文介绍了什么是不可变的基础设施，它与旧式可变基础设施之间的概念和实践差异，使用它的优势，以及它的实现细节。

知道是否或何时应该考虑迁移到不可变的基础设施可能很困难，而且没有一个明确定义的界限或拐点。一种开始的方法是实现本文中推荐的一些设计实践，比如配置管理，即使您仍然在一个很大程度上易变的环境中工作。这将使将来向不变性的过渡更加容易。

如果您有一个包含上述大部分组件的基础架构，并且您发现自己遇到了扩展问题或者对部署过程的笨拙感到沮丧，那么这可能是开始评估不变性如何改进您的基础架构的好时机。

你可以从几家公司(包括 [Codeship](https://blog.codeship.com/immutable-infrastructure/) 、 [Chef](https://blog.chef.io/2014/06/23/immutable-infrastructure-practical-or-not/) 、 [Koddi](https://www.koddi.com/developing-with-an-immutable-infrastructure/) 和 [Fugue](https://fugue.co/assets/docs/Immutable_Infrastructure_Fugue.pdf) )那里了解更多关于不可变基础设施实现的内容。

在 DigitalOcean 上构建、测试和部署一些新的东西——一体化云平台开发人员及其团队的最爱。为新用户提供 100 美元的免费帐户信用:[do.co/devto](http://do.co/devto)T3】

[![Creative Commons License](img/4662fd03838b335557c183fd57c6b02c.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/) 
本作品受[知识共享署名-非商业性使用-类似共享 4.0 国际许可](http://creativecommons.org/licenses/by-nc-sa/4.0/)的许可。