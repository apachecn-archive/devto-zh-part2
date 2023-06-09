# 创建 Terraform 提供者——第 1 部分

> 原文：<https://dev.to/nathmclean/creating-a-terraform-provider---part-1-3i28>

从[媒介](https://medium.com/spaceapetech/creating-a-terraform-provider-part-1-ed12884e06d7)交叉发帖，这是我为我的雇主博客[太空猿科技](https://medium.com/spaceapetech/)写的帖子

Ops 中一个相当常见的任务是弄清楚如何以编程方式管理服务；使用用户界面开始一项服务通常很容易，但这不会扩展，最终，我们会希望对服务的配置有更多的控制；Terraform 提供者可以成为给你这种控制的工具。

要创建一个 Terraform 提供者，你只需要编写管理资源的创建、读取、更新和删除(CRUD)的逻辑，Terraform 会处理剩下的事情；状态、锁定、模板语言和管理资源的生命周期。

只需看一看现有供应商的列表，你就会发现 Terraform 是多么的多功能。该列表包括云提供商、Kubernetes、DNS 服务、GitHub、监控工具和 TLS 证书提供商等。

在 [Space Ape](https://spaceapegames.com) 时，我们决定开始以编程方式管理度量服务[波前](http://wavefront.com)的配置。我们都准备编写一个新工具，允许我们在 YAML 模板化警报和仪表板，然后通过他们的 API 在 Wavefront 中创建它们。当 Hashicorp 宣布发布 Terraform v0.10.0 的[版本时，我们已经开始为 Wavefront API 编写](https://github.com/hashicorp/terraform/blob/v0.10.0/CHANGELOG.md) [Go 客户端](https://github.com/spaceapegames/go-wavefront)，该版本将提供者从 Terraform 核心中分离出来，允许每个提供者独立于 Terraform 开发和发布，并允许创建自定义提供者。

所以我们着手建立波前地形供应商。

### 如何建立自己的提供商

为了避免在这篇博文中使用真正的 API，以便您可以根据自己的意愿进行操作，我创建了一个小 API 来存储(想象中的)“项目”。该 API 允许您创建、读取、更新和删除项目。一个项目有一个名称、描述和标签列表。项目的名称必须是唯一的。关于 API 的工作我就不多赘述了，你可以在这里找到更详细的描述[以及](https://github.com/spaceapegames/terraform-provider-example/blob/master/Readme.md)[terraform-provider-items/API](https://github.com/spaceapegames/terraform-provider-example/tree/master/api)中的代码。

Terraform 提供者用来与 API 交互的客户端不应该在提供者内部实现，因为这将使提供者过于复杂，并意味着客户端不能用于其他目的。所以我也在 [API 包](https://github.com/spaceapegames/terraform-provider-example/tree/master/api/client)中写了一个客户端。

示例提供者和 API 的完整源代码可以在 [GitHub](https://github.com/spaceapegames/terraform-provider-example) 上获得。

#### 入门

我们将在这篇博文中构建的提供者将允许我们使用以下 Terraform 代码在我们的服务器上创建一个项目:

[![](img/7adc2c7fdbae40493165293e4e6245f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OAiu-J67--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyFSDdnXt1mBwq1qC1D5RdA.png)

Terraform 插件是 Terraform 通过 RPC 与之通信的二进制文件。可以用任何语言编写一个提供者，但实际上，您会希望用 Go 编写它；Terraform 在 Go 中提供了助手库来帮助编写和测试提供程序。

您的提供者所在的存储库(因此也是目录)的名称很重要；所有提供程序都以`terraform-provider-`开头，其后的任何内容都代表提供程序的名称。在这种情况下，我会选择非常有想象力的`terraform-provider-example`。

接下来，我们将创建`./main.go`，它将作为我们的提供者的入口点。`main.go`只是用来调用我们的提供者，我们将在一个单独的包中实现它，在本例中称为`provider`。