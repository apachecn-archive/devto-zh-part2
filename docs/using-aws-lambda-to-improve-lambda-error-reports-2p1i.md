# 使用 AWS Lambda 改进 Lambda 错误报告

> 原文：<https://dev.to/hoelzro/using-aws-lambda-to-improve-lambda-error-reports-2p1i>

*原贴于[hoelz . ro](https://hoelz.ro/blog/using-aws-lambda-to-improve-lambda-error-reports)T3】*

我用 AWS Lambda 做很多小事情，比如我自己的音乐浏览器 T1 和 T2 跟踪 Twitter 帖子的回复 T3。我真正喜欢 Lambda 的一个用途是确保我的服务器上一切正常。我的意思是，为什么要付钱给 Pingdom 这样的服务来做我用 20 行 Python 脚本就能完成的事情呢？

关于 Lambda，我不喜欢的一点是当事情失败时，我通过 SNS 得到的错误消息:

```
{  "version":  "0",  "id":  "3a26baa3-e818-8bc4-62cf-cb7ab71757c9",  "detail-type":  "Scheduled Event",  "source":  "aws.events",  "account":  "123456789",  "time":  "2018-05-02T12:12:11Z",  "region":  "us-east-2",  "resources":  [  "arn:aws:events:us-east-2:123456789:rule/every15minutes"  ],  "detail":  {}  } 
```

不太直截了当，是吧？那就是*印刷精美的*！我不仅不知道到底哪里出了问题(我的检查功能检查多项内容，如服务器是否启动，TLS 是否设置正确，
搜索是否返回结果，等等)，而且我还有多个功能使用相同的 SNS 主题来通知我，所以我甚至不知道*哪个*功能失败了！
对于前者，我可以随时登录 AWS 控制台查看日志，对于后者，我可以交叉引用不同功能的 Cloudwatch 错误警报，但如果修复只是简单的“登录到您的服务器并重新启动服务”，那就需要做很多工作。我想一定有更好的办法！

嗯，正如你从标题中所料，确实有！最棒的是，我可以使用另一个 Lambda 函数来修复 Lambda 糟糕的错误报告。在这篇文章中，我们将介绍 Lambda 如何处理死信队列的错误，查看 Cloudwatch 日志，并发现一些在为 Lambda 开发时可能会遇到的陷阱。

# 死信队列

你需要知道的第一件事是 Lambda 函数如何报告错误，如果你还不知道的话。通过异步事件(比如 Cloudwatch 事件——把它想象成一个 cronjob 计时器)调用的 Lambda 函数会重试三次——如果这些尝试都不成功，事件就会被转发到所谓的*死信队列*(又名 DLQ)。AWS 中的死信队列可以是 SNS 主题或 SQS 队列——我选择了前者，因为它很容易连接到我的电子邮件。这就是上面 JSON 的“错误消息”blob 的来源——它是 Cloudwatch 事件启动我的 Lambda 函数的事件。

Lambda 函数可以从 Cloudwatch 事件之外的源调用；另一个潜在来源是——你猜对了——一个 SNS 话题！因此，我需要做的就是编写另一个 Lambda 函数来拦截死信队列消息，格式化一封漂亮的电子邮件，并以我的方式发送出去。

# 拦截失败

我做的第一件事是建立一个名为`lambda-failures`的新 SNS 主题——我不想用这些新消息污染我用于 SNS 通知的旧主题，尤其是在我工作的时候。下一步是将 my ping 函数的 DLQ 指向这个新主题，然后创建一个新的 Lambda 函数来接受关于这个主题的消息。然后我在我的`$LATEST`版本的 ping 函数中引入了一个故意的失败(Lambda 允许你对你的函数进行版本化，并给版本分配别名——类似于 Git 标签和分支。阅读 [AWS Lambda 函数版本和别名](https://docs.aws.amazon.com/lambda/latest/dg/versioning-aliases.html)了解更多信息！).现在一切都连接好了，我们需要做的就是点击 ping 函数上的“Test”按钮来产生一个错误，对吗？

所以，我点击了测试按钮——函数*确实失败了，但是我没有收到发送给我的新 Lambda 函数的消息。怎么回事？*

我花了一段时间才弄明白，但亲爱的读者，我就不跟你说了。您可能还记得我在上面对死信队列的描述，dlq 涉及到由*异步*事件触发的 Lambda 调用——但是它们*不*涉及同步调用！单击 test 按钮会创建一个同步调用，它不会向 DLQ 发送失败消息(这个想法是由调用者负责处理错误)。

因此，为了说服我的函数使用它的 DLQ，我创建了一个“每分钟一次”的 CloudWatch 事件，并将其与我的新 Lambda 函数连接起来(回想起来，使用`aws lambda invoke --invocation-type Event`手动调用函数会更简单)。现在我有异步调用发生了！为了加速我的开发和测试，我转储了发送给新函数的事件，并将其保存为测试事件——这样我只需单击“test”就可以测试我的函数了！

# 提取日志消息

AWS Lambda 自动将函数的日志输出发送到 Cloudwatch 日志中——这里的下一个技巧是提取特定调用生成的日志，并使用它们创建格式化的错误消息。根据我的工作经验，我知道如果您向 Cloudwatch Logs 发送一条解析为 JSON 的消息，您可以编写 JSONPath 查询来搜索日志流中的匹配条目。因此，我做了一点工作，让我的 ping 函数将所有内容记录为 JSON，使用一个`message`键表示实际消息，使用`id`表示 Cloudwatch Events 给我的事件 id。

之后，我编写了一些代码，从通过 DLQ 转发给我的函数的事件中提取事件 ID，并在 Cloudwatch 日志中查找该事件 ID。我没有授予我的函数 Cloudwatch logs 读取权限，所以我仍然需要将它们授予 IAM 中函数的角色。一旦你这样做了，你需要等待一段时间让新的权限在 Lambda 中生效。

我需要弄清楚要检查哪个日志组——Lambda 对每个函数使用一个日志组。我没有找到一种简单的方法来确定基于 Cloudwatch 事件的函数，所以现在我将它硬编码为总是查看 ping 函数的日志。您可以定制 Cloudwatch events 在调用 Lambda 函数时使用的有效负载，但我想我会在需要时添加它。

# 消息一式三份

完成后，我注意到一些奇怪的事情——我收到了一式三份的日志消息！这让我有点困惑，但我记得在 Lambda 将事件放入 DLQ 之前，一个函数会被尝试三次！

# 吸取教训&前进

因此，我们学到了一些关于 AWS Lambda 的经验:

*   您可以将 SNS 用于 dlq，也可以用作 Lambda 调用的触发器
*   dlq 只在异步调用期间涉及，比如 Cloudwatch 事件，并且只在三次失败尝试之后
*   如果您以 JSON 身份登录，可以对 Cloudwatch 日志使用相当复杂的查询

我肯定可以在这方面做一些改进——首先，我想动态地发现哪个函数失败了，并使用它来确定要查询的正确日志组。我想为未来的 Lambda 开发做的另一件事是检查类似于 [Zappa](https://www.zappa.io) 的东西——我已经
检查过[无服务器](https://serverless.com)，但是我犹豫了，因为你需要给它一个拥有非常广泛权限的 IAM 用户。