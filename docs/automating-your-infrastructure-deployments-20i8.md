# 自动化您的基础架构部署

> 原文：<https://dev.to/fedekau/automating-your-infrastructure-deployments-20i8>

*最初发表于 [WyeWorks 博客](https://wyeworks.com/blog/2018/6/11/automating-your-infrastructure-deployments/)。*

我们都知道，现代应用程序可以变得非常复杂，非常快。正因为如此，我一直试图在我的日常生活中增加更多的自动化，因为我知道我是人，最终会因为分心、疲劳或其他事情而陷入困境。

在我的自动化之旅中，我做的第一件事就是找出一种方法来自动化云资源的创建。为了做到这一点，我开始熟练使用基础设施代码(IaC)工具，如 Terraform 和 AWS CloudFormation 等。

在此之后，下一个合乎逻辑的步骤是将基础设施供应方面的知识集成到 CI/CD 管道中，因为我已经(或试图)在我参与的每个项目中设置了 CI/CD 管道。在这篇文章中，我将展示这样做的好处，并用一个具体的 Terraform + CircleCI 例子来演示。

## 自动化基础设施的优势

如果你不习惯自动部署基础设施的概念，这可能听起来很疯狂，但在我看来好处远远大于风险。主要好处如下:

*   部署将是自动的。
*   当你做出改变时，你会变得更加细致。
*   在对基础设施进行更改时，您会更加谨慎，这几乎总是会减少停机时间。
*   您永远不会忘记在部署之前更新您的分支。
*   你的发展和反馈循环将会更短；你合并你的修改，所有的东西都会为你所用。无需等待“运营”团队来帮助您。
*   如果您需要为一个部署执行多个步骤，您只需一个接一个地合并多个 PRs。
*   您可以通过在部署之后(或者如果您使用[蓝/绿部署](https://martinfowler.com/bliki/BlueGreenDeployment.html))添加一些自动冒烟测试来自动测试您的基础设施。
*   可以投入更多的时间开发功能。
*   可以在管道中跟踪所有部署。

## 拥有自动化基础设施的风险

就像生活中的所有事情一样，这种方法也有缺点。然而，通过一些团队训练或简单地添加一些安全机制，它们很容易被克服。

让我们回顾一下一些风险:

*   你的团队必须意识到自动化过程。如果你合并了一个公关，那是因为你想合并。如果您不小心合并了代码，它将被应用。为了解决这个潜在的问题，您可以为`production`环境添加一个手动的部署确认。
*   您可能会忘记如何手动部署。这很容易解决——只需不时地手动执行部署过程。

现在，我们已经了解了基础设施自动化的利弊，让我们来看一个具体的例子。

# CI/CD 带 Terraform 和 CircleCI

我创建了一个[范例库](https://github.com/fedekau/terraform-with-circleci-example)，其中包含了一些想法，你可以用它们来启动你的项目或者让你当前的项目变得更好。存储库包括一个详细的自述文件，解释了我做出每个决定的原因。

主要目的是展示使用 IaC 工具(如 [Terraform](https://www.terraform.io/) 和持续集成工具(如 [CircleCI](https://circleci.com) )管理基础设施的众多方法中的一种。

我遵循了《Terraform: Up 运行》和《T2》circle ci 2.0 文档中描述的许多最佳实践。我还假设你对这些工具和[亚马逊网络服务](https://aws.amazon.com)有一定的了解(尽管不需要成为专家)。

## 建议工作流程

假设您有两个环境，`production`和`staging`:当一个新的特性被请求时，您从`staging`分支，提交代码并打开一个 PR 到`staging`分支。之后，CircleCI 将运行两个任务，一个为林挺，另一个将计划对您的`staging`基础设施的更改，以便您可以查看它们(见下图)。

[![Image of PR creation jobs](img/8b88ba9729bc76b2b1f76b9b0864479d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FJBjBq2U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fedekau/terraform-with-circleci-example/staging/.images/pr.png)

一旦您合并了 PR，如果一切按计划进行，CircleCI 将运行您的作业并自动部署您的基础架构！

[![Image of jobs after staging merge](img/266c5defe12c7caa46f70db5a02416d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aGIeoNqK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fedekau/terraform-with-circleci-example/staging/.images/staging-merge.png)

在`staging`中测试完您的基础设施后，您只需打开一个从`staging`到`master`的 PR，将您的基础设施“推广”到`production`。

在这种情况下，我们希望有人手动批准向 master 发布，因此在您合并后，您需要告诉 CircleCI 它可以继续，它将在收到确认后部署基础架构。

[![Image of jobs after master merge](img/f3f17aa4d9a84c992b01de54a545c911.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t8A214kv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fedekau/terraform-with-circleci-example/staging/.images/master-merge.png)

## 改进

这是一个非常基本的工作流程，有许多地方必须(或者可以)改进。这里有几个例子:

*   在我看来，需要做的最重要的改进是在将它们与`staging`合并之前，为每个 PR 添加一个测试基础设施的方法。或许类似于 [Heroku 评论应用](https://devcenter.heroku.com/articles/github-integration-review-apps)的东西会是最好的。如果你使用 Terraform 模块，并以一种通用的方式编写它们，你可以非常容易地实现基础设施测试。
*   添加测试！这当然是每个项目中必须的。如果你想要质量，你需要测试。你可以使用像 [KitchenCI](https://kitchen.ci/) 或 [InSpec](https://www.inspec.io/) 这样的工具来完成这项工作。

# 结论

我总是喜欢在我的文章中提出一些最终的、非常普遍的观点，所以这里有一些:

*   尝试更多的自动化。这将引导你的团队提高效率，减少出错几率。
*   使用 IaC 工具。这样做将有助于所有部分的可维护性和理解。
*   找到解决问题的方法；不要让问题塑造你。