# 运行一个使用 AWS Lambda 构建的大规模电子邮件平台需要多少成本？

> 原文：<https://dev.to/jsitapara/how-much-it-costs-to-run-a-mass-emailing-platform-built-using-aws-lambda-4je5>

《纽约时报》每年发出 40 亿封电子邮件。这是一个巨大的数字！虽然我们不会发出这么多电子邮件，但我们每年会发出 50 万封电子邮件，其中大部分是事务性电子邮件和时事通讯。

去年，我们建立了一个供内部使用的电子邮件平台。我们想要的是一个简单易用的用户界面平台。我们成功了将近半年。

但是后来，

*   费用
*   表演
*   可量测性

因此，我们转向 AWS Lambda，并发誓不使用任何传统服务器。截至今天，我们使用 AWS Lambda 作为我们的计算服务，使用无服务器框架，用 Node.Js 编写，完全由事件驱动。因为我们都知道 AWS Lambda 如何提高性能和可伸缩性，所以在这篇文章中，我将谈谈我们如何节省资金。

在迁移到 AWS Lambda 之前，我们在 EC2 上完全正常运行。我们使用 t2 .小实例，花费我们

**$0.023 * 24 小时* 30 天* 12 个月= $198.72**

[![IMAGE](img/45e1aff1635e74fde2309113a6282710.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---JaSmVgQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.simform.com/wp-content/uploads/2018/08/Serverless-Email-Example-using-SES.png)

借助 AWS Lambda 和简单的电子邮件服务 SES，我们构建了一个内部且经济高效的解决方案。它是这样工作的，

*   有了 S3，我们存储我们的 CSV 文件，您可以快速发送 HTML 或基于文本的电子邮件给大量的收件人。
*   我们将 CloudWatch 与 AWS Lambda 集成在一起，后者会根据预设的触发条件定期发送简讯。
*   或者每当用户在 S3 上传一个 CSV 文件，它就会触发我们的 Lambda 函数，通过 SES 发送电子邮件。

要多少钱？

*   Lambda 的费用为 0 美元，因为每月 1M 的请求是免费的。
*   4.10 美元，再见
*   标准 1GB S3 存储 0.02 美元。

总计 49.44 美元/年。听起来好得难以置信，对吧？但事实并非如此。这只是无服务器成本的冰山一角。我们编辑了一份关于 **AWS Lambda 定价的[详细分析](https://www.simform.com/aws-lambda-pricing/):运行一个无服务器应用程序要花多少钱？**

此外，AWS Lambda 允许你做一些异常的事情。如果您想知道从哪里开始，我们已经为您准备了一份详细的列表: **10 AWS Lambda [用例](https://www.simform.com/serverless-examples-aws-lambda-use-cases/)开始您的无服务器之旅**

欢迎分享你的经验和建议。在 Twitter [@jsitapara](https://twitter.com/JSitapara) 上联系，谈论任何关于无服务器、AWS Lambda 以及任何介于两者之间的东西。