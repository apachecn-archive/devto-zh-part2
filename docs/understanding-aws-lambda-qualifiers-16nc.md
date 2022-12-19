# 了解 AWS lambda 限定符

> 原文：<https://dev.to/kyleparisi/understanding-aws-lambda-qualifiers-16nc>

# 场景如下

您有一个正在使用 AWS cloudwatch 日志的应用程序。您需要一种非常简单的方法来解析这些日志中的错误，并向监控 slack 通道发送消息。我们有几个日志流(服务 1、服务 2 等)用于两种不同的环境。一个用于试运行，一个用于生产。这些环境应该向各自的松弛通道发送消息。

# 使用限定词

限定符是命名代码不同版本的一种方式；例如生产、登台等..AWS lambda 限定符有利于划分环境，也有利于在升级生产之前升级阶段中的代码版本。限定符允许唯一的触发器。由此，我们附加了 staging 限定符来观察一组流。例如:

[![lambda example](../Images/f1bbca33606288c7e679e2bca835a1d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B6PyypEg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/toon039i7z0p4w4kbbg9.png)

从上面的图片中，你可以看到我已经为我的代码版本 1 设置了限定符，基于不同的文本模式有 2 个 cloudwatch 日志触发器。每个流都可以通过点击一个按钮来打开和关闭，而无需重新部署。我觉得这个超级酷。

不幸的是，您不能基于限定符设置环境变量。只有版本化的代码才会被赋予静态环境变量。但是，身边有一个工作。

```
function isNotAnAliasName(context) {
  return isNumber(context.alias) || context.functionName === context.alias;
}

// Add this to your exports.handler
context.alias = context.invokedFunctionArn.split(":").slice(-1)[0];
console.log("Alias: " + context.alias);
// Set a default value
if (isNotAnAliasName(context)) {
  context.alias = "UAT";
} 
```

Enter fullscreen mode Exit fullscreen mode

在那里，您可以命名您的环境变量，如下所示:

```
const slackPostPath = process.env["SLACK_POST_PATH_" + context.alias];
const slackBotUsername = process.env.SLACK_BOT_USERNAME + "  " + context.alias; 
```

Enter fullscreen mode Exit fullscreen mode

### [代码](https://github.com/kyleparisi/cloudwatch-logs-slack)

代码可能看起来很密集，但这是因为没有依赖关系。用链接代码的副本建立一个 lambda 并保存它。设置您想要的限定符以及适当的触发器和松弛时间详细信息，然后！廉价的日志监控。