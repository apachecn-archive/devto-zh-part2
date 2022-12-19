# AWS Lambda 常见问题

> 原文：<https://dev.to/johndemian/aws-lambda-faq-1ji6>

[![Alt text of image](img/5dfd5864d27d14b9bfd16a81eaa0fa17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UXRZodNe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/356079/pexels-photo-356079.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D750%26w%3D1260)

你听说过 AWS Lambda 吗？它是一种新的计算系统，可以为你提供各种好处，比如实时数据处理和定制后端服务。希望你知道这个系统的来龙去脉，这样你就可以决定它是否适合你？

有这么多关于 AWS Lambda 的信息，如果没有 AWS Lambda FAQ，很难知道它是什么。

## AWS Lambda 常见问题

好奇 AWS Lambda 是什么，它能为你做什么，它会让你和你的企业付出多少代价？我们是来帮忙的。以下是关于该系统的常见问题分类，因此您可以确定它是否适合您。

## 什么是 AWS Lambda？

AWS Lambda 是一个 Amazon [无服务器计算系统](https://medium.com/@pmuens/aws-fundamentals-what-is-lambda-32d17a89dda2)，它运行代码并自动管理底层计算资源。它允许您自动运行代码来响应许多类型的事件，比如来自 Amazon API gateway 的 HTTP 请求、Amazon DynamoDB 中的表更新和状态转换。它还使您能够使用定制逻辑扩展到其他 AWS 服务，甚至创建自己的后端服务。

该服务通过在高可用性计算机基础设施上运行您的代码来工作。然后，它会执行该计算资源的所有管理任务，例如维护您的服务器和操作系统，自动扩展和管理您的容量供应，以及处理安全补丁部署和代码监控及记录。

你这边唯一需要做的工作就是为它的运行提供代码。

## AWS Lambda 是如何工作的？

虽然 AWS Lambda 一开始看起来令人困惑，但事实并非如此。事实上，它实际上只是一个 4 步的简单过程。

从上传代码到 AWS Lambda 开始。在那里，设置您的代码从其他 AWS 服务、HTTP 端点或移动应用程序触发。AWS Lambda 只会在你的代码被触发时运行它，并且只使用运行它所需的计算资源。

什么更好？您只需为您使用的计算时间付费！

## AWS Lambda 有什么优势？

AWS Lambda 为用户提供了多种好处。在决定 AWS Lambda 是否适合你时，以下是你应该知道的 3 个最大的好处:

### 它不需要您管理任何服务器

由于 AWS Lambda 会自动运行您的代码，因此您无需管理服务器。简单地写下你的代码并上传到 Lambda，你就可以开始了。

### 它使您能够轻松扩展

AWS Lambda 运行代码来响应每个触发器，因此您的应用程序会自动伸缩。您的代码也是并行运行的，并单独处理每个触发器，因此可以根据工作负载的大小精确地进行伸缩。

### 它是负担得起的

有了 AWS Lambda，当代码不运行时，您不需要支付任何费用。您只需为代码执行的每 100 毫秒以及代码实际被触发的次数付费。

## 用 AWS Lambda 可以构建什么？

AWS Lambda 可以构建各种东西。下面是它的几个使用案例的快速列表:

*   数据处理

*   实时文件处理

*   数据有效性

*   过滤

*   整理

*   实时流处理

*   第三方 API 请求

如您所见，AWS lambda 可以完成许多事情。要了解更多关于 AWS Lambda 的功能，请访问此页面。

## AWS Lambda 多少钱？

如前所述，有了 AWS Lambda，你只需为你使用的东西付费，将请求的数量和代码的持续时间考虑在内。Lambda 认为请求在每次开始执行时都是对触发器的响应，比如事件通知或[调用量](https://dashbird.io/features/aws-lambda-serverless-monitoring/)。

代码的持续时间从代码开始执行的时刻开始计算，直到代码返回或终止。

如果您不确定 AWS Lambda 是否适合您，有一个免费的层选项可供尝试。该选项包括每月 100 万个免费请求，以及每月 400，000 GB 秒的计算时间。

如果你想了解更多关于 AWS Lambda 的信息，请查看亚马逊便利的 [AWS Lambda 常见问题](https://aws.amazon.com/lambda/faqs/)。

## 关于 AWS Lambda 的最终想法

如果你想简化流程，减少编码工作，你绝对应该投资 AWS Lambda。通过查看 AWS Lambda FAQ，您可以了解这个计算系统的详细情况，并确定它是否适合您。

一旦你完成了常见问题，你可能会开始怀疑你的无服务器应用程序的可观察性，或者更好地说，缺乏可观察性。你不会是第一个这样想的人，但幸运的是，有许多服务可以在这方面帮助你。以 [Dashbird.io](http://dashbird.io) 为例，它们可以帮助您监控您的无服务器环境，设置警报并跟踪成本，而不会给您的应用程序增加任何负担。此外，他们有一个相当不错的免费层，足以让你开始。你可以[现在注册](https://dashbird.io/register/)开始免费监控你的无服务器应用！