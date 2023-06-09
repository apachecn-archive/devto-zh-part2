# 去做吧！带 AWS Lambda

> 原文：<https://dev.to/rapasoft/just-do-it-with-aws-lambda-4nla>

## 前传

去年我做了一个关于 AWS Lambda 的演示。这不是我在日常工作中使用的东西，但我想进一步探索这项技术，并将其展示给公众。当我为这次演讲准备代码示例时，我遇到了一个问题，那就是想出一些超出标准的“Hello World”或其他人的示例的真实世界的用法。演讲还不错，但缺少了那种“哇！”观众意识到这是改变游戏规则的时刻。

快进到现在:我们公司最终采用 Slack 作为我们的主要通信工具。与传统的 Skype for Business 相比，这是一个巨大的进步，主要是因为我们可以创建专门的频道。因此，最常用的渠道是...关于去哪里吃午饭的讨论！

## 斗争

你看，这在斯洛伐克是件大事，因为餐馆以优惠价格提供每日午餐菜单。我们办公室附近的几家餐馆每天都有不同的菜单。有很多选择，所以你明白为什么午餐讨论是一个持续的。

第一个想法(在 Slack 时代之前)是创建一个 web 应用程序，从附近的餐馆收集菜单信息。无论是从他们的网页还是从提供这些信息的类似服务。于是，午餐者诞生了！

[![Luncher](img/a0c919aba291144c073983124b459de1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kZhkhrHJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e35k6fjjox98tl8ilf32.png)

因此，我们的午餐流程是:

1.  从启动器创建屏幕截图
2.  发布到松弛`#lunch`频道
3.  创建`/poll`去哪里吃午饭。

这并不是很懈怠(如果这是一个词的话)。

## 想法

幸运的是，我设计了 Luncher 来公开一个 REST API，它以 JSON 格式提供了一个日常菜单列表。我的第一个想法是创建一个 slack 命令并使用这个 API。在没有任何先验知识的情况下，我开始探索事物是如何工作的。

在 Slack 中，创建命令相当容易。您只需在组织内创建新的应用程序，然后为其提供功能。在我的例子中，目的是创建所谓的斜杠命令，它以斜杠开头，后跟命令名和参数。我把这个命令叫做`/obed`(斯洛伐克语的意思是午餐)。

[![Implementing obed](img/71f376c36f30916332a18807c4248338.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0sgUzJC5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kojjx49sjfck5m2zif5a.png)

## 转折

创建 slash 命令的规则非常简单:您使用`/obed`命令，它将调用 REST API 来获取数据并在 Slack 中列出结果。这很简单，但问题是(问题来了)Slack 要读取的 JSON 需要特定的格式，比如:

```
{
    "text": "Here's the main text!",
    "attachments": [
        {
            "text":"Here be some additional text!"
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

launcher 以一种非常不同的格式发送响应。

最简单的方法是添加额外的端点，以 Slack-friendly 方式发送响应，但是真正的问题是。Luncher 作为一个 WAR 文件被部署到 Azure(它是 Kotlin + Spring Boot ),在那里修改任何东西都有点痛苦，因为我不负责部署。

## 解

当我决定尝试实现一个 AWS Lambda 函数来调用 Luncher API 并将输出转换为 Slack 格式时，我没有想太久。整个实现真的很简单，因为我使用 awesome [Serverless](http://serverless.com) 框架创建并部署了 Lambda 函数。

基本上，当执行`/obed`时，它将调用 AWS API Gateway 中定义的端点，该端点将指向 Lambda 函数。这个函数将调用 Luncher REST API，检索数据，在 Slack JSON 响应中转换数据并返回。结果你会得到这个:

[![Ta-daaa](img/cb5fd626f6c40c470f1ffcd3c1029de8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vx9vQWd_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lkqrh3rho73on4jic3oj.png)

## 最后的话

最棒的是它超级简单。我设法在一个小时左右完成了这件事。目前它很简单，但是可以进行增强，以便以一种更奇特的方式接受参数和格式化输出。此外，现在我有了一个现实世界的例子，我的下一个 FaaS 相关的主题。