# 什么是无服务器，为什么它很重要？

> 原文：<https://dev.to/rdlauer/what-is-serverless-and-why-does-it-matter-f0n>

让我们不要自欺欺人，直到[花衣魔笛手的“新互联网”](https://www.wired.com/2017/06/pied-pipers-new-internet-isnt-just-possible-almost/)上市，让数据中心变得无关紧要，世界仍将在服务器上运行。无论是在桌子下面还是在云容器中，服务器仍然是服务器。

我们当中有多少人与非技术人员的朋友和家人交谈过，问我们什么是*云*以及他们是否应该“在”云中？队列眼球滚动和一个简洁的解释如何**云实际上只是别人的服务器**。

将“无服务器”与 IaaS、PaaS、SaaS 和 FaaS 等首字母缩写混在一起，即使是我们中最优秀的人也会头晕目眩😵。

## 云简史

今天，我们大多数人都意识到了云的价值以及它为从业余爱好者到大型企业的每个人提供的投资回报。在前云时代，当部署应用程序时，开发人员必须考虑:

*   购买服务器；
*   找到一个合适的地方安置它；
*   将其连接到互联网；
*   安装和修补操作系统；
*   保护信息技术(如物理安全、防火墙、DDoS 防护等)；
*   ...然后是硬件和软件的长期维护。

然后出现了像 Rackspace 这样的公司(以及其他许多公司)，它们承诺卸下服务器物流的责任。但是到了最后，这些提供商只解决了问题的一部分。创建特定于地理位置的故障安全集群、维护服务器软件以及从 dev - > test - > QA - > prod 镜像环境仍然留给我们这些普通人。

**进入☁️.**

随着虚拟化技术的改进，Rackspace 之类的公司随意加速虚拟机的能力成为了新的标准。我们不再调配物理硬件，而是发出命令，双击新服务器和新环境。

哪里有技术可以赚钱，你可以肯定的是，技术巨头就在不远处。亚马逊的 AWS、微软的 Azure 和谷歌的云平台击败了竞争对手，成为卓越的云计算服务提供商，或者更好地称为 IaaS(基础设施即服务)提供商。

这些公司已经将集中式计算资源从本地服务器(甚至只是服务器机柜)转移到全球分布式数据中心。考虑创建一个包含 x 台服务器的集群环境来支持您的应用。如今，只需很少甚至没有前期成本和少量 CLI 命令就可以实现这一目标。

## IaaS，PaaS，FaaS，SaaS，天啊！

今天的云最好被描述为一组相互有效构建的服务。

[![saas paas iaas pyramid](../Images/96f3d3291abe978c012ca9555958f217.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--33C3Nndo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/serverless/pyramid.png)

### 基础设施即服务(IaaS)

2010-2012 版本的云实际上只是 IaaS(基础设施即服务)。亚马逊建立了惊人的基础设施来支持他们自己的业务。一些邪恶的天才意识到，他们可以也应该向公众转售这些服务(2017 年收入超过 170 亿美元💰).

因此，您可以将这些担忧转移到云上，而不是构建和管理自己的数据中心。到目前为止都说得通！😊

### 平台即服务(PaaS)

我们的云金字塔的下一层是 PaaS(平台即服务)。你问 PaaS 是什么？好问题！良好的...嗯...🤔

我认为 PaaS 的最佳方式是将您的应用程序部署到预先配置的环境中。作为开发人员，您不必担心底层基础设施、存储或网络问题。但是你可以控制应用程序本身和环境配置。

PaaS 无疑是云计算中定义最模糊的部分，但也蕴含着最多的机会。你可能会说 Azure 和 [Progress Kinvey](https://www.progress.com/kinvey) 是以 PaaS 为中心的，但它们都提供了超出典型 PaaS 定义的服务。

### 软件即服务(SaaS)

在传统的云模型中，SaaS(软件即服务)位于顶部。这是软件开发的终极游戏。每月付费提供给无数用户的数字位。受欢迎的 SaaS 产品包括微软的 O365、谷歌文档、MailChimp、Salesforce，甚至是 NativeScript Sidekick 的云构建产品。它们是高性能的分布式服务，几乎可以立即启动，不需要任何本地配置或安装。

### 功能即服务(FaaS)

但是等等，FaaS(功能即服务)在云金字塔中处于什么位置呢？那这个“无服务器”的东西呢？

让我们停止谈论 FaaS(尽管它非常符合我们的缩写模型)，开始谈论**无服务器**。实际上，它们是一回事。

> 你可以很容易地认为无服务器比 FaaS“更大”或“更多”,但是对于本文的范围，我们不要想太多。

### 无服务器

想想今天的一个移动应用。一个负责任的开发者会挑选一个像 [NativeScript](https://www.nativescript.org/) (无耻的插头)这样的框架😄)在 iOS 和 Android 之间共享代码。添加一个需要自己的托管环境的 web 变体。可能有一个数据库在某处，NoSQL 或关系。也许是信息服务。撞车监控服务。你明白了。

尽管为所有这些服务构建环境比以往任何时候都更容易，但这并不意味着它很容易。PaaS 提供商消除了很多痛苦，但也有服务器端业务逻辑(也称为云代码)需要处理。这就是无服务器的好处。这只是你的代码，运行在云中。

**当然，无服务器不代表没有服务器。**无服务器的概念弥补了应用程序部署的最后一个缺口，使开发者完全无法做出任何决策。**你编写代码并部署它。故事结束。**一切都为您配置和管理。

无服务器的区别在于开发变得关注于单独的功能而不是服务。从 [Kinvey FlexServices](https://devcenter.kinvey.com/guides/flex-services) 的角度考虑。这些是低代码和轻量级的“微服务”，处理应用程序的服务器端业务逻辑。

例如，如果您想使用 Google URL Shortener API，您可以这样做:

```
function shortenURL(context, complete, modules) {
  const requestOptions = {
    uri: 'https://www.googleapis.com/urlshortener/v1/url?key=',
    body: {
      longUrl: context.body.longUrl
    },
    json: true,
    resolveWithFullResponse: true
  };
  _getConfig(complete, modules).then((result) => {
    requestOptions.uri += result;
    request.post(requestOptions, (error, res, body) => {
      if (error) {
        return complete().setBody(error).runtimeError().done();
      }
      complete()
        .setBody({ shortUrl: body.id })
        .done();
    });
  });
}

exports.shortenURL = shortenURL; 
```

Enter fullscreen mode Exit fullscreen mode

...这里有完整的实现。

**在[GitHub 资源库](https://github.com/remotesynth/flex-service-samples)中找到更多 Flex 服务示例。**

这些功能存在于云中，并在可预测、可伸缩且可靠的环境中执行。开发人员利用这些功能构建系统，以最分散、最强大的方式将应用程序拼凑在一起。

> 阅读更多关于[开始使用 Kinvey FlexServices 的信息](https://www.progress.com/blogs/getting-started-with-kinvey-flexservices)

## 原生角度

虽然移动应用通常通过应用商店发布，当然也在移动设备上运行，但几乎每个应用都有一个支持后端。因此，当你构建下一个 NativeScript 应用时，考虑一下 [Kinvey](https://www.progress.com/kinvey) 的无服务器功能。您可以构建一个更好的应用程序，其功能包括:

*   🏃快速访问[遗留数据库](https://devcenter.kinvey.com/guides/rapid-data)；
*   🔌轻松连接到[企业认证](https://www.progress.com/blogs/enterprise-authentication-kinvey)系统；
*   🍃为数据集成和业务逻辑编写[低代码、轻量级微服务](https://devcenter.kinvey.com/guides/flex-services)的能力；
*   📲[iOS 和 Android 的推送通知](https://devcenter.kinvey.com/guides/push)；
*   📂[数据](https://devcenter.kinvey.com/guides/datastore)和[文件](https://devcenter.kinvey.com/guides/files)存储；
*   👩‍💻基于角色的[用户管理](https://devcenter.kinvey.com/guides/users)。

**享受 Kinvey 的 100%免费层，解锁这些功能，构建更强大、更安全、可扩展的应用程序。**

[今天试试金威](https://console.kinvey.com/signup)。你可能需要的东西都是免费的！