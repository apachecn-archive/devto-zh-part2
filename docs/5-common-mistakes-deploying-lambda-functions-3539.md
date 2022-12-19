# 部署 Lambda 函数的 5 个常见错误

> 原文：<https://dev.to/paulswail/5-common-mistakes-deploying-lambda-functions-3539>

如果您是构建功能即服务应用程序的新手，尤其是 AWS Lambda，那么在部署最初的几个功能时，您可能会遇到几个障碍。下面的列表希望能确保你不会犯和我一样的错误。

## 1。不使用部署工具包

手动上传代码包，然后在 AWS 控制台中配置它们既慢又容易出错。如果您使用部署工具包，如[无服务器框架](https://serverless.com)，这将自动完成所有的打包和部署，并允许您的 Lambda 配置受到版本控制，相关功能将被分组到同一个部署包中。

## 2。设置和忘记内存分配

您需要为每个函数调用分配多少内存付费，这样您就不会希望分配比完成任务所需内存多得多的内存。然而，这并不是故事的全部。随着内存分配的增加[，AWS 分配给函数](https://docs.aws.amazon.com/lambda/latest/dg/resource-model.html)的 CPU 能力也会增加。这意味着，通过分配更多的内存，你的函数可以更快地完成(尤其是在 CPU 受限的情况下)，从而减少你的执行时间。

这将涉及一些尝试和错误，以获得最佳分配点，并且您应该从过度分配的一侧开始(比如大约 512MB)。
一旦您的函数被部署，您的 Lambda 调用会被自动记录到 CloudWatch，在那里您可以看到类似下面的一行，您可以使用它来比较最大已用内存和内存大小(已分配):

```
REPORT RequestId: be2fc81e-8345-11e8-b334-cb4ce1d5d651  Duration: 30914.88 ms
Billed Duration: 31000 ms Memory Size: 256 MB   Max Memory Used: 111 MB 
```

Enter fullscreen mode Exit fullscreen mode

这个特殊的例子展示了一个使用了不到一半分配内存的函数。如果您知道这个函数是 IO 绑定的(例如，它将一个事件交给另一个 API)，那么这将是减少分配的一个很好的候选，因为它可能不需要分配给它的所有内容。[无服务器框架](https://serverless.com/framework/docs/providers/aws/guide/serverless.yml/)默认分配 1024MB 给一个函数，如果你的函数只是做一些简单的直通网络调用，这可能太多了。

然而，如果你的函数要做一些计算量更大的事情，那么你应该尝试增加内存分配，看看它是否能降低总成本。参见本文中的[第 1 点，了解 Lambda 性能测试的示例，展示更大内存分配的好处。](https://www.jeremydaly.com/15-key-takeaways-from-the-serverless-talk-at-aws-startup-day/)

## 3。没有考虑如何管理秘密

AWS 确实允许您将秘密(密码、API 密钥、数据库连接字符串等)作为环境变量传递，当您部署 Lambda 函数时，这些环境变量使用 AWS KMS 加密存储在静态环境中。
然而，在部署时，您仍然需要告诉 AWS 从哪里获取这些环境变量的值。显然，您不希望将它们硬编码到您的部署脚本中。

推荐的方法是使用[系统管理器参数存储库](https://aws.amazon.com/blogs/compute/sharing-secrets-with-aws-lambda-using-aws-systems-manager-parameter-store/)来存储您的秘密。无服务器框架[等部署工具包允许您将参数存储键映射到环境变量键](https://serverless.com/blog/serverless-secrets-api-keys#handling-secrets-for-small-teams--projects)，它将在部署时读取环境变量键，并在 Lambda 的配置中加密存储。

## 4。Node.js 函数中的超时

如果您使用 Node.js 实现 Lambdas，那么您的函数有可能会超时，即使看起来已经完成了它们的处理。

这是因为 JavaScript 事件循环可能尚未清除(例如，如果下游函数中有未处理的回调)。当这种情况发生时，您将结束支付整个时间段的费用，直到函数超时(默认超时为 3 秒)，而不仅仅是进行实际处理的时间。

要解决这个问题，您应该始终将以下内容设置为处理函数中的第一行代码:

```
context.callbackWaitsForEmptyEventLoop = false; 
```

Enter fullscreen mode Exit fullscreen mode

## 5。不设置预算预警

这个错误并不是 Lambda 特有的，但是如果你是 AWS 开发的新手，你最不想做的事情就是积累一份意想不到的账单。由于 Lambda 非常容易启动和运行(尤其是在使用部署工具包时)，您可能会意外地触发函数按固定时间表运行，直到月底账单到来时您才意识到这一点。

为了避免这种情况，[创建一个预算提醒](https://aws.amazon.com/premiumsupport/knowledge-center/create-monthly-budget/),这样一旦达到阈值，你就会收到电子邮件。

💌 ***如果你喜欢这篇文章，你可以注册[到我关于在 AWS](https://winterwindsoftware.com/newsletter/) 中构建无服务器应用的每周时事通讯。***