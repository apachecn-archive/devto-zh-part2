# 启动 CliX:用 JavaScript 编写的无服务器 Pusher 竞赛条目

> 原文：<https://dev.to/kayis/startup-clix-serverless-pusher-contest-entry-written-in-javascript-1jai>

在经历了一个漫长的周末之后。

[![me getting wrecked](../Images/6eb489667629a2a8e5085c6100b6f815.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XhKTR15d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mwqbtaz8m2r0o3tlera3.png)

我终于找到时间为参赛作品写一篇合适的博文。

# 我造了什么

我用[推手](https://pusher.com/channels) & [AWS 山姆](https://github.com/awslabs/serverless-application-model)做了一个多人点击器游戏。

## 玩法

你从 30000 美元的种子资金开始。

每轮游戏中，你都要尽可能快地点击并提交你的产品，从而创造出*个产品*。

你的产品价格是由`500000 * clicks / milliseconds`计算的

由于创业公司的烧钱速度，你每轮还会损失 5000 美元。

所以你很可能每一轮都要赔钱，哈哈。

如果你输掉了所有的钱，你就出局了。

最后剩下的玩家获胜。

## [试玩链接](https://kay-is.github.io/startup-clix/)

## [链接到代码](https://github.com/kay-is/startup-clix)

# 我是如何建造的

## 堆栈

我的想法是完全不需要服务器。虽然 Pusher 允许无服务器设置，但遗憾的是它没有无服务器定价模式，呵呵。

我用的是 AWS SAM，它是 CloudFormation 的扩展，所以除了 Pusher，它是 100%的基础设施作为代码。

用于 HTTP 请求的亚马逊 API 网关。加入游戏，每轮提交产品，接收推手 webhook 数据。

[AWS Lambda](https://aws.amazon.com/de/lambda/) 用于所有的服务器端计算。检查空游戏，计算每轮的利润，通过游戏事件(`round:start`、`round:end`等)的推送器通知玩家。

[AWS 步骤功能](https://aws.amazon.com/de/step-functions/)游戏配合。开始游戏，开始回合，每回合调用 Lambda 计算东西，通过 Pusher 通知玩家。

[Amazon DynamoDB](https://aws.amazon.com/de/dynamodb/) 存储游戏数据(产品、玩家数量)，以便状态机控制的 Lambda 函数可以访问这些数据。

推送通道将数据从后端发送到客户端，无需任何轮询。

前端内置 [create-react-app](https://github.com/facebook/create-react-app) 和[Bootstrap](https://getbootstrap.com/)(v4+[bootstratch Sketchy](https://bootswatch.com/sketchy/))。

## 建筑

我还做了一个架构图:

[![Game Architecture](../Images/710a1d8ef62a77be6a09644bc96a98c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c2ERc960--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9a3ny9hiwkxuf8uoyxsd.jpg)

如您所见，数据从播放器和推送器通过 HTTP 进入我的无服务器后端，并通过推送器通道反馈给客户端。

## 推子通道的作用

推送通道允许向客户端广播/多播事件，而无需直接连接到客户端。

您只需调用 Pusher HTTP-API 来发送一个事件，Pusher 就会负责将其分发给客户端。

这很酷，因为 WebSockets 会强迫你保持与所有客户端的开放连接，这对于 Lambda 函数来说是不可能的。他们只能跑大约 5 分钟。

当一个 Lambda 函数被调用时(通过 API-Gateway 或 Step 函数),它可以简单地完成自己的工作，向 Pusher 发送一个 HTTP 请求，然后再次挂起，而 Pusher 跟踪打开的连接。

Pusher API 还允许通过 HTTP 获取所有通道的状态，因此您可以启动一个 Lambda，检查谁在线，如果愿意，还可以根据通道状态发送数据。

## 问题

### 慢实时

Pusher 宣传其*频道*为**实时**，但事实并非如此。

它的核心使用网络技术，所以它完全建立在 TCP **和**之上，它在整个架构中至少增加了一个服务器(=网络跳)。

首先是从 Pusher 到客户机的 WebSockets 连接，它比每个事件的完整 HTTP 请求有更少的延迟，但仍然会带来一些往返。

第二，您使用服务器端的 HTTP API 向 Pusher 发送事件，这导致了`client -HTTP-> back-end -HTTP-> Pusher -WebSockets-> client`的延迟。

它的独特卖点更多的是通过*非常*简单的设置(因此得名 Pusher，哈哈)将数据推送到客户端，但不是最小的延迟。

它允许您使用客户端事件作为中间人来减少您的后端，从而进一步减少延迟，但是您不能为每个事件在推送方运行代码，这大大降低了有用性。

因此，目前我们谈论的是每秒不到 10 个事件。这对大多数应用来说已经足够了。

这就是为什么我不把每次点击都发送到服务器，而是每一轮都收集它们。这允许每 10 秒钟一个 HTTP 请求(10 秒一轮)

类似于 Lambda 的 Pusher 事件，在 Pusher 基础设施上运行将是一个杀手级的特性，呵呵。

### 单向步进功能

下一个问题是 AWS 阶跃函数。我发现模拟游戏状态和回合等相当不错。但是我还没有找到一种方法来轻松地将数据**放入**状态机。

问题如下:

你定义了一个状态机。这个状态机可以执行多次。

状态机的每一个状态都可以调用带有输入的 Lambda 函数*或者*创建一个活动任务。

我的想法是将游戏定义为一个状态机，状态机的每次执行都是一个运行中的游戏。

虽然每个执行都可以等待一个工作器(例如 API-Gateway Lambda)完成一个活动，但是工作器不能过滤每个执行活动中的任务。

所以我不能使工人执行/游戏特定。

我必须添加 DynamoDB 来将数据放入状态机。

玩家通过 HTTP (API-Gateway -> Lambda)发送一个成品，后端将它存储到 DynamoDB 中，`gameId`是主键。

当状态机决定一轮结束时，例如在 10 秒的`Wait`-状态之后，它启动一个 Lambda 函数来查看 DynamoDB，计算结果并将它们发布给客户端。

DynamoDB 有很好的并发特性，所以还不算太差，最终可以更容易地同步玩家。

# 附加资源/信息

我也为这个过程写了一堆博文。

*   [推手大赛创意:一款 JavaScript 游戏](https://dev.to/kayis/pusher-contenst-idea-a-javascript-game-12j0)
*   [推手大赛创意:JavaScript 游戏(2)](https://dev.to/kayis/pusher-contest-idea-javascript-game-2-228d)
*   [推手大赛创意:JavaScript 游戏(3)](https://dev.to/kayis/pusher-contest-idea-javascript-game-3-2m1l)
*   [启动 Clix:带 AWS SAM 的推动器存在通道](https://dev.to/kayis/startup-clix-pusher-presence-channels-with-aws-sam-4li0)
*   [启动 Clix:清理&与推焦器战斗应用状态](https://dev.to/kayis/startup-clix-cleanup--fighting-with-pusher-application-state-2ilc)
*   [启动 Clix: ESLint &用推动器应用状态](https://dev.to/kayis/startup-clix-eslint--winning-with-pusher-application-state--923)获胜
*   [启动 Clix:AWS 步骤功能的第一步](https://dev.to/kayis/startup-clix-first-steps-with-aws-step-functions-2o35)
*   [启动 Clix:预认证&带推送器的 web hooks](https://dev.to/kayis/startup-clix-pre-authentication--webhooks-with-pusher-1g99)
*   [启动 CliX: DynamoDB &在 GitHub 页面上反应前端](https://dev.to/kayis/startup-clix-dynamodb--react-front-end-on-github-pages-18ol)
*   [启动 CliX:终于有些玩法了](https://dev.to/kayis/startup-clix-finally-some-gameplay--4le4)
*   [创业 CliX: RC1 带私服游戏&手机 UI](https://dev.to/kayis/startup-clix-rc1-with-private-games--mobile-ui-1hmd)

# 结论

使用我的新后端技能并看到自己现在真正做全栈的东西是很有趣的。

我学到了很多关于 Pusher，AWS 和无服务器的知识。

我会感谢你所有的喜欢，独角兽什么的，但即使我没有赢，也很高兴把这个项目添加到我的投资组合中(也许在清理之后，哈哈)

此外，一如既往，问题和拉请求是受欢迎的。也许有人有办法让这个游戏变得有趣，呵呵。

* * *

* * *

## 我欣赏你所有的喜欢和独角兽！