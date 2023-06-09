# 用 Pulumi 和 Typescript 编程云

> 原文：<https://dev.to/alexito4/programming-the-cloud-with-pulumi-and-typescript-4hkf>

*这篇文章最初发表在我的博客上。*

近年来，服务器端的开发和基础设施的管理已经取得了长足的进步。从易于管理的可扩展虚拟机到 Docker 的容器化，甚至是 Amazon Lambda 的无服务器功能。但是所有这些进步仍然需要你改变思维模式，从编程你的应用到配置基础设施。

即使它比以往任何时候都容易，但它仍然不是没有摩擦的，当你只想尝试一个快速的想法或在周末项目中工作时，你最不想要的就是摩擦。所以我一直对[普鲁米](https://www.pulumi.com)很好奇。我关注[乔·达菲](https://twitter.com/funcOfJoe)有一段时间了，因为他在编程语言方面的工作很有趣，他最近在**云编程**方面的工作非常有前途。

> Pulumi 提供了一个云本地编程模型来创建容器、无服务器功能和云基础设施，具有不可变基础设施和真正编程语言的所有优点。

这太棒了。我非常相信一些配置应该用真正的编程语言来完成，Pulumi 将这个想法带到了云基础设施中。

所以这个周末我决定尝试一个我一直想实现的想法，使用 Pulumi 似乎是最快的方法。

## 无服务器

对于这个想法，我只需要一对夫妇的端点和一个小数据库。我最近在工作中与 Amazon Lambda 和 DynamoDB 一起工作，我知道 Amazon 提供了这些服务的免费层，所以它似乎是周末项目的理想选择。

即使无服务器有很多优势，你仍然需要设置相当多的东西。Lambda 函数、发电机表、用户角色、策略、API 网关...这不是 5 分钟就能学会的。对我来说幸运的是，我有几个很棒的导师帮助我在工作中快速启动它，但我仍然没有足够的信心在所有的设置上自己做。

## 电池组

这就是普鲁米的用武之地。在安装了 CLI 并设置了 AWS 凭证之后(这是您需要直接与 AWS 打交道的唯一部分，您会厌倦仅仅创建一个帐户😂)您可以创建一个项目并开始处理您的应用程序。

Pulumi 编程模型将基础设施资源视为代码中的对象。所以如果你想要一个 API 端点，你只需要在你的代码中创建一个！

```
const endpoint = new cloud.API("langs"); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！这就是你要用 Lambda 拥有一个工作的 API 网关所要做的一切。最好的事情是，您可以将该对象视为 web 服务的路由器，就像在任何其他 web 框架中一样。

```
endpoint.get("/test", async (req, res) => {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，您就在 API 网关中配置了一个“/test”端点，您可以运行从 lambda 函数添加到闭包中的代码。

这是 Pulumi 的另一个优势。因为这些代码创建了基础设施并拥有您的应用程序逻辑，所以您可以通过访问它们的变量，甚至在闭包中捕获它们，轻松地在基础设施的不同部分之间进行通信！

这意味着您可以轻松地从 Lambdas 访问 DynamoDB 表，只需从端点闭包内部调用表对象上的方法！

```
const endpoint = new cloud.API("langs");
const table = new cloud.Table("projectsTable", "project");
endpoint.get("/test", (req, res) => {
    table.get({ 'project': "1" }).then(value => {
        res.status(200).json(value)
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

Pulumi 通过序列化 lambda 闭包中捕获的对象并在运行时透明地传递它们来实现这一点。

很棒的是，您不必担心设置您需要的服务，甚至不必担心如何在它们之间传递数据。你只需要专注于你想要创造的产品。

## 强大的基础设施配置

到目前为止，您已经看到了仅仅通过创建对象就能够访问基础设施是多么酷，特别是对于像我这样不是 devops 专家的人来说。但是对于专家们来说，建立基础设施的普鲁米也实在是太棒了。

首先，您拥有真正编程语言的所有能力。条件句，[循环](http://blog.pulumi.com/program-the-cloud-with-12-pulumi-pearls#pearl-1)，函数...任何事。

拥有所有这些能力将基础设施的重用带到了一个新的层次。你可以轻松地[创建和分发组件](http://blog.pulumi.com/program-the-cloud-with-12-pulumi-pearls#pearl-2)，说到底只是一个库！

为了了解这有多神奇，我举了一个真实的例子。我仍然不相信 Lambda 的一点是冷启动。Lambda 函数实例可以在几分钟不活动后关闭并重新使用。这可能会增加相当多的延迟，特别是当您访问移动网络中的这些端点时。通常这不是你需要担心的事情，但有时有一个系统不断 ping lambda 来保持它的温暖是有用的。

这种设置可能非常复杂，但是看看这篇文章，看看如何构建一个[可重用组件来保持 AWS Lambda 的温暖](https://mikhail.io/2018/08/aws-lambda-warmer-as-pulumi-component/)。

只需使用组件 Pulumi 将设置所有需要保温的基础设施:

```
const lambda = new mylibrary.WarmLambda("my-warm-function", { /* options */ }, handler); 
```

Enter fullscreen mode Exit fullscreen mode

## 打字稿

另一件我不想处理的事情是，你猜对了，Javascript。我很清楚，如果你使用所有的现代工具，这种语言并没有那么糟糕，但我仍然是一个快速的爱好者，我需要我的编译器😘

幸运的是，Pulumi 支持 Typescript，在我看来这是最好的语言之一。事实上，您可以编写一个混合了安全检查语言和疯狂 javascript 动态的程序，这是一个非常酷的成就。

即使我喜欢我的类型，在处理 JSON API 时，我也不会太想念它们，我只需要从中获取一些值，特别是在一个附带项目中。在一个更严肃的编程中，我可能会增加打字和更多的安全性，但现在我不必这么做，这很好。

## 结论

普鲁米很棒。我不是基础设施方面的专家，所以这个观点是双向的。事实上，它允许我部署所有的服务，而不必检查如何配置它们，这太棒了。但是因为我不是这方面的专家，可能有些东西你会说不理想。也许吧。大概吧。

无论如何，这个项目进展得相当快。它们支持不同的语言，如 Javascript/Typescript、Python 和 Go(我希望有一天 Swift 也包括在内)，以及不同的云提供商，如 AWS、Google Cloud、Azure 等。即使我专注于无服务器，因为这是我正在使用的，你也可以使用容器或任何其他类型的基础设施。

如果你对这些东西感兴趣，不要怀疑，有了免费的 AWS 账户，你就可以免费在^^学到所有的东西