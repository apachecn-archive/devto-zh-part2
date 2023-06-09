# 更好的部署之旅

> 原文：<https://dev.to/nathmclean/a-journey-to-better-deployments-31e3>

从[媒介](https://medium.com/spaceapetech/a-journey-to-better-deployments-a255eb69bbf2)交叉发帖，这是我为我的雇主博客[太空猿科技](https://medium.com/spaceapetech/)写的帖子

[![](img/75ec1d0599d30aa49197ec155669c3d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b11LWaIf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOuFo5e3J3oy_PATxAnWTHw.jpeg)

在过去的一年里，我们一直在努力改进我们的部署流程。在那段时间里，我们构建了定制工具，并开始使用新的(和旧的)工具和实践。这篇文章将尝试概述我们为什么想要一个新的部署过程，我们如何着手实施它，以及我们现在有什么。

### 旧制度

#### 基础设施

我们的基础设施是使用 CloudFormation 构建的。CloudFormation 模板由 [CFNDSL](https://github.com/cfndsl/cfndsl) 生成，这些模板又被包装在一个内部的 Ruby Gem 中，这使得它更容易使用。

CloudFormation 堆栈通常会产生一个自动扩展组，由一个传统的负载平衡器提供支持，并具有相关的安全组和 IAM 角色和配置文件。

CloudFormation 还用于建立我们 AWS 基础设施的其他部分，如 VPC 网络、CloudFront 分发和 S3 桶。

我们所有的实例都由厨师管理。Chef 每 15 分钟运行一次，按照我们的要求配置我们的系统，它控制着机器的各个方面。这意味着管理操作系统的配置，安装系统软件包，并确保我们的实例保持最新和打补丁。它还负责安装和配置我们的应用程序。

### 部署

所有的部署都是由主厨策划的。当部署发生时，它由我们控制，并以以下三种方式之一发生:

1.  可以设置环境，在每次运行 Chef 时获取最新的可用包。当 Jenkins 构建完成时，我们用它来部署我们代码的最新版本。
2.  部署到其他开发环境是使用 Web UI 或通过 CLI——Knife 插件进行的(两者都有效地使用相同的 Ruby 底层代码),它告诉 Chef 部署特定版本的代码和配置。
3.  生产部署是通过开发人员机器上的命令行使用刀插件驱动的。这允许对部署进行更高程度的控制。例如，部署通常包括正在部署的金丝雀节点，开发人员在决定是否继续部署之前检查日志和指标。如果部署继续进行，那么将启动对剩余实例的滚动部署。否则，金丝雀被回滚。

### 为什么要开发新的部署流程？

#### 更快的部署

每个部署都需要一次完整的 Chef 运行，这意味着我们要检查系统的所有其他部分，只是为了安装一个 Jar 和一个 YAML 配置文件并重启一个服务。

从 CI 部署花费的时间太长，Chef 每 15 分钟运行一次，这意味着在新版本发布后，我们平均要等待 7.5 分钟才能开始部署。开发人员希望尽快看到他们的代码是否有效。

生产部署花费了很长时间:

1.  我们从负载平衡器中取出一个实例
2.  冉大厨
3.  将实例添加回负载平衡器
4.  等待开发人员检查日志
5.  对其余实例重复步骤 1-3(或回滚)

#### 更安全的部署

Chef 管理的服务器是可变的——它们会随着时间而变化。我们从来没有 100%确定我们是否可以从零开始构建一个新实例到当前状态，当机器(不可避免地)死亡时，或者当我们需要扩展新实例时，这可能会导致问题。

生产部署是从开发人员的机器上运行的，这意味着有许多因素会导致部署出现问题，例如:

*   开发人员安装了所有的先决条件吗？
*   他们有正确的 AWS 证书吗？
*   他们的网络连接稳定吗？如果它在部署过程中中途退出会怎么样？

我们非常依赖开发人员检查日志和指标来确认我们正在部署的新版本是否按预期工作。这其中的大部分可以自动化。

### 新系统

#### 目标

我们对部署系统的要求如下:

1.  部署应该在几秒或几分钟内完成(而不是几十分钟或更长)
2.  部署不应该从开发人员的机器上运行
3.  我们应该部署不可变的实例
4.  尽可能多地自动执行部署后检查

#### 第一相

由于我们想要不可变的实例，很明显我们需要将应用程序代码烘焙到 AMIs (Amazon 机器映像)中。然后我们需要协调这些非盟特派团的部署。我们决定使用 [Spinnaker](https://www.spinnaker.io/) 来做这件事。Spinnaker 是“一个开源、多云的持续交付平台，用于以高速度和信心发布软件变更”。

本质上，它允许您定义一个管道，定义您希望部署如何发生。

最小管道由烘焙阶段和部署阶段组成。部署阶段可以由许多不同的内置[部署策略](https://www.spinnaker.io/concepts/#deployment-strategies)组成(例如红色/黑色、金丝雀或 Highlander ),或者您可以定义自己的部署策略。您还可以随意添加其他步骤，例如添加回滚的可选步骤或可以执行检查的步骤。

很明显，通过使用 Spinnaker，我们将能够实现我们的目标:

*   使用 Spinnaker 部署烘焙的 ami 应该很快
*   安全，因为我们可以通过环境推广 AMI
*   我们的实例是不可变的
*   我们也没有从自己的机器上运行这些部署

但是我们在自动化部署检查方面做得还不够。

我们很快发现，虽然 Spinnaker UI 非常适合我们的 DevOps 团队使用，但对于许多用户(只对部署代码感兴趣的开发团队成员，而不是部署如何工作的细节)来说，其中有太多不必要的信息。

我们决定构建一个系统来包装 Spinnaker(以及任何未来的部署类型),以便抽象出 Spinnaker 等工具的一些复杂性，并覆盖开发团队使用的一些术语。

我们选择构建一个基于 Ruby on Rails 的 API，它将存储关于我们的环境和服务的信息，并允许我们使用后台工作人员来跟踪部署状态。

在 API 之前，我们构建了一个 React 应用程序，它充当 API 的主要用户界面。

#### 停下来思考一下

我们现在有了一个工作的部署系统，但是在我们继续之前，我们花时间停下来和我们的用户回顾我们的进展。人们已经习惯了我们旧的部署系统，就像任何变化一样，很难调整。在开发系统时，许多看似直观的东西最终并不适合用户。

在这个过程中，我们想出了一些我们想要改变的事情:

*   第一次部署的时间(在我们的应用程序的 CI 构建之后)仍然太长。烘焙 AMI 可能需要几分钟，这意味着我们只比 Chef 部署的实例稍微快一点。
*   我们的 Web 界面需要一些工作来变得更加直观，更好地向用户显示信息。
*   工具之间的集成可能会更好。例如，Jenkins jobs 直接开始部署 Spinnaker，跳过我们自己的工具。

这一反馈导致了许多改进。

### 第二阶段

#### “触发”部署

为了加快首次部署时间，我们不得不做出一些妥协。我们意识到我们永远不会让 AMI 烘焙得足够快，所以为了速度的利益，我们打破了我们最初的要求之一——不可变实例。

我们开发了一个部署策略，我们称之为触发部署。Trigger Deploy 的完整工作方式可以独立完成一篇完整的博客文章，所以我在这里不做过多的描述。

本质上，我们通过 SNS 和 SQS 向长期存在的实例传递消息，以部署包的新版本。每个实例监听队列中感兴趣的消息，然后对其进行操作。

这意味着我们现在可以在代码的 CI 构建成功后几秒钟内完成部署。但是我们更容易出错，在这种情况下，我们觉得这是一个可以接受的妥协。

#### 网络界面

我们在面对面的会议中以及通过工具中嵌入的反馈按钮积极征求反馈，从用户那里收集大量关于如何改进 UI 的反馈。例如，在完成某些操作后，我们将用户导航到哪里，以及我们如何布置信息以便于阅读，在提供用户需要的所有信息和不使它们过载之间取得平衡。

我们还引入了其他信息，例如环境的当前状态。

这是一项持续的工作。通过会议、反馈表和临时转换来收集用户反馈并采取行动意味着我们一直在改进系统。

#### 工具集成

我们希望我们的部署 API 成为所有部署的中心信息源——部署应该通过 API 启动、控制和监控。一种方法是通过 React UI。但有时这还不够——例如，如果我们希望 Jenkins 在 CI 构建后开始部署，我们需要以编程方式与 API 进行交互。

当然，这可以通过 API 实现，但是处理认证和设置每个 Jenkins 作业比直接调用 Spinnaker 更困难(Spinnaker 作业可以设置为从 Jenkins 构建中触发)

因此，我们构建了一个 CLI，这让我们能够将 Jenkins 直接调用 API 的大部分痛苦抽象化，并且还为用户提供了使用 CLI 而不是使用 UI 的选项。

#### 将（行星）地球化（以适合人类居住）

我们也开始使用 Terraform，而不是 CloudFormation 来建造基础设施。这给我们带来的主要好处之一是能够使用远程状态在堆栈之间共享信息，而不是拥有一个全局配置文件。其他好处包括使用模块，这给了我们一个比旧的 CloudFormation 模板更好的界面。

使用 Spinnaker 的一个副作用是我们的堆栈稍微简单了一些，因为我们不必创建自动缩放组，因为 Spinnaker 会为我们管理它。

### 应用程序接口

[![](img/bbdfe6276fc7d310ae7a04bf1b8060f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4OHTYZ8K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/797/1%2AsGXFPkLnJnQ7eWCO4zRDAA.png)

API 现在已经成为将我们的部署结合在一起的粘合剂。它是所有部署相关活动的真实来源。

它不仅编排了 Spinnaker 和“触发”部署，而且我们可以随时添加新的部署类型。我们目前有使用 SAM 部署 AWS Lambda 应用程序和运行 T2 terra form 的原型。

我们还在研究如何通过 API 管理我们的服务配置。

由于 API 处于我们部署工作流程的中心，我们已经能够利用它来创建许多工具，如 React frontend、Golang CLI 和基于 Lambda 的 Slack bot。

### 结论

到目前为止，我们的工作成果是部署速度更快，在新版本的代码发布后，只需几秒钟即可完成部署。

下一个环境需要几分钟的时间，但是我们更有信心部署会成功。

Prod 部署不再从用户的机器上运行，并且比以前更快。在自动检查部署运行状况方面，我们还没有取得我们希望的进展，这是我们将继续努力的方向。

经历这一过程凸显了定期检查用户是否满足他们的要求是多么重要。

这只是更好部署之旅的开始，我们已经打下了坚实的基础，我们可以在此基础上管理不同的部署类型，并继续使部署更快、更安全。

* * *