# 推手竞赛创意:一款 JavaScript 游戏

> 原文：<https://dev.to/kayis/pusher-contenst-idea-a-javascript-game-12j0>

正如你可能读到的，第一届[开发者竞赛](https://dev.to/devteam/first-ever-dev-contest-build-a-realtime-app-with-pusher-4nhp)将于本月举行，作为一名狂热的开发者博客作者，我想，也许我应该提出一个想法。我不知道我是否有时间实施它，虽然我这个月没有项目，但我要去参加一个节日，并做第三个也是最难的 AWS 助理认证。

## 什么

所以我想了想怎么办？

有用的东西？哦...

创新的东西？Muh...

有趣的东西？大概吧！

人们喜欢娱乐！

你是人，我是人，这样大家都会开心。

但是！

还不到一个月呢！

所以我需要一个简单的游戏...

我听说每个人都喜欢遥控器，这可能是所有游戏中最简单的。你点击，生活变得更好。

此外，因为这是一个推内容，它必须是多人游戏。

那么现在中的**是什么类型的多人游戏呢？**

是的，你想的没错:战斗罗伊尔

所以，是的，一个战斗风格的点击游戏。

## 如何

在亚马逊 S3 上托管客户端。

推送者[私人频道](https://pusher.com/docs/client_api_guide/client_public_channels) & [客户事件](https://pusher.com/docs/client_api_guide/client_events#trigger-events)可以将点击量分发给所有参与者。

Pushers [web hooks](https://pusher.com/docs/webhooks#client_events) 可以和[AWS Lambda](https://aws.amazon.com/de/lambda/features/)&[AWS API Gateway](https://aws.amazon.com/de/api-gateway/)一起使用来跟踪点击服务器端。

AWS DynamoDB 可以跟踪每个游戏的点击量，ElastiCache 也值得一试，但我猜 Lambda、API Gateway 和 DynamoDB 将是用 [AWS SAM](https://github.com/awslabs/serverless-application-model) 安装最快的。

[AWS 步骤功能](https://aws.amazon.com/de/step-functions/)可以处理游戏状态。如果我理解正确的话，它基本上是一个状态机，为它进入的每个状态触发特定的 Lambda 函数。这些函数可以通过 HTTP 向 Pusher API 发布游戏事件。比如游戏开始、游戏结束、玩家出局等。

## 问题

由于冷启动，Lambda 功能可能太慢。

而且在无服务器的环境下乱搞游戏状态会变得相当麻烦，我从来没有用过 AWS 的 Step 函数，哈哈。

游戏可能会很无聊，哈哈

## 结论

好主意，一条通向解决方案的道路，也许有足够的时间来设计和实现它。

让我看看...