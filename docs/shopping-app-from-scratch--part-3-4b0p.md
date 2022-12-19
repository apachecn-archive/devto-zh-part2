# 从头开始的购物应用——第 3 部分

> 原文：<https://dev.to/samwsoftware/shopping-app-from-scratch--part-3-4b0p>

我被要求为一次工作面试制作一个购物网站的原型。这是流程的第 3 部分。阅读 [*第一部分*](https://medium.com/@samwsoftware/creating-a-shopping-app-from-scratch-part-1-352ebbd78655) *和* [*第二部分*](https://medium.com/@samwsoftware/creating-a-shopping-app-from-scratch-part-2-5e5ec24b2e0b) *如果你还没有*。

已经有了一个不错的网站，离我的面试还有 8 天，我决定开始开发聊天机器人。我从来没有和机器人一起工作过，所以我真的很兴奋。

它始于大量的谷歌搜索，我找到了无数的方法来创建一个聊天机器人。其中一个教程是关于聊天机器人在 Medium 上的生活。本教程使用 claudia-bot-builder 创建了一个机器人，并将其部署在 AWS 上。

我按照自己的方式完成了教程，但是当我使用 Claudia 将它部署到 AWS 时，我遇到了一个问题。它不承认我的 AWS 证书。我搜索了 stackoverflow、AWS 和无数论坛，但无法排序。我花了几乎整个上午试图让它工作。如果你有 AWS 或克劳迪娅评论的经验，让我知道我应该做什么！

[![](../Images/87dfb79d1703d50507a0e9cbc0d03025.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xNl6itMA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/479/1%2AU0ScankV5e__HqUEDn5-Hw.png)

下一个教程我发现没有使用 Claudia 或 AWS，所以我试了一下，它成功了。我必须从使用 ngrok 将 webhook 请求通过隧道传输到本地端口开始。

有了这个设置，我需要在我的 bot API 中创建 webhook 路由。这意味着为脸书验证创建一个 get 路由，为接收任何消息创建一个 push 路由。

现在，我必须完成设置脸书的过程，以便它能与我的新机器人 API 一起工作。这包括为我的小组创建一个脸书页面，向该页面添加一个应用程序，向该应用程序添加消息，然后将我的 ngrok 路由添加到已验证的消息路由。

我验证了路由，看到请求通过。我还决定通过提交请求，让我的应用程序获得 bot 消息的批准。这使得机器人可以回复其他用户，而不仅仅是我。

下一步是创建一个简单的函数，将相同的消息发送回用户。这很棒，因为它让我看到了流程是如何工作的，并发现了任何错误。幸运的是没有，而且效果很好。

有一个模仿你说什么的聊天机器人很酷，但完全没有用。为了给这个机器人一个目的，你可以将它连接到 API.ai。可能有许多不同的自然语言处理(NLP)库和资源，但这是教程建议的一个。

一旦我注册了，我就创建了一个代理。代理就像机器人的逻辑，它包含所有的“请求→响应”路径。首先，我添加了一个“闲聊”插件，这意味着机器人可以与用户聊天。

<figure>[![](../Images/0809fbbc7656b4c7e6304c7b93b751c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lgrQ-fhW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/404/1%2A8bLaIWbuAHFkki6OlqRjCw.gif) 

<figcaption>闲聊</figcaption>

</figure>

你可以定制任何反应，它有一个输入列表，将导致这一反应。巧妙之处在于，短语不必完全匹配。如果用户键入“你叫什么名字”而不是“你叫什么名字”，它仍然匹配。这是使用 NLP 服务而不是自己硬编码所有响应的真正优势。

### 打造更加个性化的产品

可用的插件很棒，看起来你可以用它们做很多事情，但有时你只需要做一些他们不提供的事情。这就是自定义意图发挥作用的地方。

### 意图

这些是任何成功的聊天机器人的组成部分。他们有一组定义好的训练短语和一个给定的响应短语。

#### 简单的文字回应

这可以简单到“我们会，我们会”得到“摇滚你！”。这最常用于简单的支持问题，如“您的客户支持号码是多少？”或者“网址是什么？”。任何可以有不变答案的问题。

#### 复杂的请求

简单的文本响应意图很有趣，但它们无法处理更复杂和相关的问题，如“我的订单在哪里”或“我可以退货吗”。对于这些，我们需要使用 webhooks。

<figure>[![](../Images/2db020837755598e1c74247b9d9bd212.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z5RmvtJD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/431/1%2ApzrzPwqUipw8u8NAkocQMw.png) 

<figcaption>履行选项</figcaption>

</figure>

当您为意图启用 webhook 调用时，数据会发送到您创建的 webhook URL。这个 webhook 获取包含在 intent 中的所有信息，您可以随意使用它，然后返回一个自定义消息。

#### 链接意图

问一个问题，然后给出一个答案，这不是一个正常的对话，这是一系列问题和答案的来回。

DialogFlow 允许您将后续意图添加到您已经制定的任何意图中。以下是我对*退货单*意向的跟进意向。

[![](../Images/b7735945e5eb33132da87c4db98e0ccf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oCBhbrDB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/474/1%2AQ7RfxxZkpe6pFSNjDIoq-A.png)

客户—我可以退回我的订单吗

Bot —当然，我可以记下您的订单号吗(简单文本响应)

客户—它是 12345678

Bot —谢谢，我只是检查一下订单(简单文本响应)

Bot —露西·琼斯的两条弗雷德·佩里 B72 系带鞋(webhook 点击 API 搜索数据库以查找订单)

Bot —这是您想要退回的订单吗？

这更像是你对客户服务的期望，在这个机器人中，这一切都是完全自动化的。

创建这个真的不是很难，我设法做到了这一点，以前从未与聊天机器人合作过。你需要一点时间来了解一切是如何工作的(特别是使用 webhooks)，但是如果你按照教程学习，并且能够使用 stackoverflow，那么你应该没问题。

webhook API 非常简单:接收一个请求，如果它有一个动作 *return order* ，然后向主 API 发出 get 请求，并以可读的方式格式化订单。

[![](../Images/ec01ff5d78883fc9588a392244993b2f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oF-2nZR8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/406/1%2AsaBmKdLmw00pTGPTTgvmAA.gif)

* * *