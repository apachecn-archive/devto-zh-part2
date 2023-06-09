# AWS 入门:在几分钟内启动并运行 Elastic Beanstalk

> 原文：<https://dev.to/kylegalbraith/getting-started-with-aws-up-and-running-with-elastic-beanstalk-in-minutes-2c8n>

如今，学习 Amazon Web Services 可能非常棘手，因为有 100 多个服务可以满足数千甚至数十万个用例。一些用例甚至可以用不止一个服务来解决！这种浩瀚的信息海洋可能会使饥渴而热情的开发人员在实际学习 AWS 平台时面临挑战。

在我最近的课程中，我们将重点放在通过实际使用来学习 AWS。我们关注 AWS 上静态网站的托管、保护和交付问题。通过关注问题，我们可以在构建解决方案时开始学习服务。对于人们来说，通过利用像 S3、CloudFront、WAF、ACM、API Gateway 和 Lambda 这样的服务来开始学习 AWS 是一件好事。

即使这样，有时我们还是想开得更快。我们只是想开始尝试 AWS，而不是陷入过多的信息中。

幸运的是，有一种服务可以帮助我们做到这一点。

弹性豆茎是一个伟大的服务浸入 AWS 游泳池你的脚趾。作为一名开发人员，它允许您带着您的工作负载，通过三个简单的步骤让它运行起来。创建一个弹性的 Beanstalk 应用程序，创建一个环境，部署您的代码，然后就可以开始运行了。

在这篇文章中，我们将采取一个样本。NET Core web API，并在几分钟内将其设置在 Elastic Beanstalk 中。

### 简要概述

在我们开始动手之前，了解一些背景知识是很有用的。

首先，Elastic Beanstalk 是一项为您提供、维护和部署底层 AWS 基础设施的服务。这很好，不熟悉 AWS 的开发人员可能不理解所有的基础设施概念，所以弹性 Beanstalk 可以让他们轻松很多。正如我们将要看到的，我们可以配置我们的环境设置，然后只需将我们的应用程序部署到其中。永远不必担心调配或维护底层基础架构。

如果在某个时候开发人员需要接管一些基础设施，Elastic Beanstalk 会提供支持。但是，如果您需要手动配置底层资源，那么使用 Terraform 这样的基础设施作为代码工具可能更合适。

出于本文的目的，我们将假设我们是云世界的新手。因此，我们将配置我们的环境，然后演示如何直接从我们的 IDE 部署到该环境。

### 资源

为了这篇文章的目的，克隆我在这篇文章中使用的代码可能对你有帮助。所以，继续克隆这个 [GitHub 库](https://github.com/kylegalbraith/elasticbeanstalk-demo)。

这个存储库包含我们需要启动和运行的 3 样东西。

*   文件夹`/src/network`包含一个`vpc.yaml`文件。这是一个用于在我们的 AWS 帐户中创建新 VPC 的云模板。这消除了配置公共和私有子网的复杂性。
*   文件夹`/src/testing`包含一个`runTests.bat`文件。一旦我们的环境运行我们的代码来填充演示数据，我们就可以运行这个脚本。
*   一个样本。NET 核心 Web API。

### 创建我们的虚拟私有云(VPC)

为了展示 AWS 最佳实践，我们将为我们的 Elastic Beanstalk 演示创建一个新的 VPC。克隆了存储库之后，让我们运行以下 AWS CLI 调用:

```
kylegalbraith:~$ aws cloudformation create-stack --stack-name demo-elasticbeanstalk 
--template-body file://vpc.yaml 
```

Enter fullscreen mode Exit fullscreen mode

此次 CLI 调用提供了一个全新的 VPC，如我们的 AWS 帐户中的`vpc.yaml`所述。该 VPC 由以下资源组成:

*   2 个私有子网。
*   2 个公共子网。*公共子网的互联网网关和路由表。
*   专用子网的 NAT 网关和路由表。

### 创建我们的弹性豆茎应用

在配置我们的环境之前，我们首先必须在 Elastic Beanstalk 中创建一个**应用程序**。应用程序拥有环境、配置和代码版本。它是弹性豆茎中的顶级对象。

要为我们的环境和代码创建应用程序，我们可以通过 aws-cli 运行以下命令。

```
kylegalbraith:~$ aws elasticbeanstalk create-application --application-name SampleWebApi 
```

Enter fullscreen mode Exit fullscreen mode

### 配置环境

一旦我们有了外部弹性 Beanstalk 应用程序，我们就可以配置我们的第一个环境。一个**环境**是我们所有 AWS 基础设施所属的容器。可以创建两种类型的环境，web 服务器和 worker。前者是我们今天的帖子所需要的，因为它将为处理 HTTP 请求提供必要的资源。工作环境用于处理 SQS 队列之外的消息。

在 Elastic Beanstalk 中有多种方式来配置环境。当您刚刚开始使用 AWS 时，最好的方法之一是通过管理控制台来配置它。

来自我们新的弹性豆茎应用。

1.  从“操作”菜单中选择“创建环境”。
2.  选择“Web 服务器环境”。
3.  点击“选择”。
4.  在“环境名称”中，输入`my-test-api`。
5.  选中“预配置平台”，然后选择“”。NET (Windows/IIS)”从下拉列表中选择。
6.  对于应用程序代码，保持“示例应用程序”处于选中状态。

在我们配置任何其他东西之前，有必要了解一下如果我们现在单击“创建环境”会发生什么。

*   将使用 URL`my-test-api.<aws-region>.elasticbeanstalk.com`创建一个新环境。
*   环境将是*单实例*。这意味着我们没有负载平衡器，在我们的自动伸缩组中只有一个 EC2 实例。
*   所有的东西都是用默认值创建的。这包括我们的运行状况检查 url、网络布局(VPC、子网等)，以及实例细节，如类型、IAM 配置文件等。

这里要带走的主要内容是，立即单击 create environment 将在 Elastic Beanstalk 中得到一个单一实例概念证明。对于我们的职位，我们想要的不止这些。

1.  因此，选择“配置更多选项”。
2.  选择高可用性预设。请注意，这将“容量”部分从单实例更改为负载平衡，这正是我们想要的。
3.  选择“更改平台配置”，然后从下拉列表中选择`64bit Windows Server 2012 R2 v1.2.0`。*注意:这是特定于您的应用程序的，对于我的演示应用程序，我必须使用 Windows Server 2012 而不是 2016。*
4.  单击负载平衡器部分下的“修改”。
5.  对于“健康检查路径”,我将输入`/api/demos`,因为这是我的 API 的默认路径。在配置您的应用程序时，您应该输入用作健康检查的 url。
6.  点击“保存”。
7.  单击网络部分下的“修改”。
8.  选择我们用云形成模板创建的`demo-elasticbeanstalk` VPC。
9.  将“可见性”选项保留为公共。
10.  对于“负载平衡器子网”，请在每个可用性分区中选择一个公共子网。
11.  对于“实例子网”，选择每个可用性分区中的专用子网。
12.  点击“保存”。
13.  点击“创建环境”。

#### 哇，踩刹车！

如果你是网络新手，你可能会在这一点上晕头转向。相信我，这很正常。出于这篇博文的目的，我将避免深入 AWS 中联网的细节，但这里是我们刚才那样配置的亮点/原因。

*   虚拟私有云(VPC)是我们 AWS 客户内部的一个逻辑隔离网络。我们有广告默认 VPC 在我们的帐户中，这是我们选择的。
*   公共子网是 VPC CIDR 区块范围内的子网络。它们是公共的，因为它们有一个连接到它们的因特网网关，允许它们与公共内部网对话。因为我们的负载平衡器需要响应来自互联网的请求，所以它们位于公共子网中。
*   私有子网没有互联网网关。然而，它可以有一个 [NAT 网关](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-nat-gateway.html)，用于与公共互联网通话。

### 环境供应

随着对网络的解释的结束，我们的环境应该在这一点上被创建。Elastic Beanstalk 正在建立我们的负载平衡 web 服务器环境所需的所有基础设施。这包括:

*   负载平衡器将接收带有运行状况检查 url 的 API 请求，以 ping 我们的实例。
*   我们的 API 实例所属的自动缩放组。
*   启动配置指定了 EC2 实例类型、安全组以及附加到自动伸缩组的更多内容。
*   我们的自动扩展组的默认扩展策略。
*   我们的负载平衡器和 EC2 实例的 CloudWatch 警报和指标。

所有这些都是由 Elastic Beanstalk 提供的，我们无需自己配置这些资源。这是服务为开发者提供的主要价值主张。您无需担心基础架构的调配、扩展或维护，一切都由您来处理。

这在你刚刚开始的时候是很棒的，但是随着你在 AWS 中的进步，你可能会超越这个需求。在某种程度上，如果您需要/想要对您的基础设施有更多的控制，您可以使用 Elastic Beanstalk 来实现，但是最好考虑真正的基础设施即代码。现在，我们将认为这是最好的入门服务，并演示如何部署到我们的新环境中。

### 从 Visual Studio 部署到我们的弹性豆茎环境

由于我们正在开发一个. NET 核心 web API，我们可以使用安装了 [AWS 工具包](https://marketplace.visualstudio.com/items?itemName=AmazonWebServices.AWSToolkitforVisualStudio2017)的 [Visual Studio 社区](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)来部署到我们新的弹性 Beanstalk 环境中。

安装并配置好这两样东西后，我们就可以部署到我们的环境中了。

1.  在 Visual Studio 中打开 dotnet core web API。
2.  在解决方案资源管理器中右击 API 项目。
3.  点击“发布到弹性豆茎”。
4.  从可用环境中选择`my-test-api`。
5.  点击“下一步”。
6.  选择“构建配置”下的发布。
7.  点击“下一步”。
8.  点击“发布”。

[![Deploy to Elastic Beanstalk from Visual Studio 2017](img/84bf8a08936e8c2f79bba4a1fa8d182c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jGE-5cgP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/kylegalbraith/elasticbeanstalk-demo/master/src/deploy-gif.gif)

接下来发生的是，我们的 API 将被编译、压缩，作为一个版本上传到 Elastic Beanstalk，然后在我们当前的配置中一次性部署到我们的基础设施中。如果我们担心停机，我们可以将部署策略从环境配置更改为滚动更新。这将逐步向负载平衡器后面的 EC2 实例推出我们代码的最新版本。

一旦我们的新版本被部署，我们应该看到我们的环境是绿色的。然后，我们可以点击我们自己的 healthcheck url 并获得成功的响应。

```
kylegalbraith:~$ curl http://my-test-api.elasticbeanstalk.com/api/demos 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

弹性豆茎无论如何都不是完美的。但是，如果您刚刚开始云之旅，这是一个很好的起点。作为开发人员，它允许您在几分钟内建立并运行基础设施来支持您的应用程序。将您从代码开发中解放出来，减少对基础设施供应和扩展的担心。

也就是说，学习和理解让云变得强大的概念非常重要。因此，很可能会有这样一个时刻，您需要理解 Elastic Beanstalk 为您提供的基础设施。事实上，你可能会到达一个需要收回控制权的点。在这些情况下，根据您的需要，考虑远离弹性 Beanstalk 是值得的。