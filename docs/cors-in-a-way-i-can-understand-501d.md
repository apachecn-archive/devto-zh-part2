# CORS，在某种程度上我可以理解

> 原文：<https://dev.to/dougblackjr/cors-in-a-way-i-can-understand-501d>

两天前，我在推特上表达了我的沮丧:

[![Tweet reads "What is CORS? CORS is added to browsers by the devil to make developers cry. Thank you for coming to my TED talk.](../Images/6edfd9b1dcc85a9e67dce775dab2eed3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sNkTmu7I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zk5es2z38nvlu1smo4ej.png)

我花了一个小时来诊断一个小小的浏览器错误，这个错误破坏了整个项目:“CORS 错误，访问控制不错的尝试，你不能这样做，哈哈”(我在转述)。

显然，它在网上引起了一群朋友的共鸣。

这让我开始思考:什么是 CORS，我该如何解释它，以便我能理解它？

## 首先，我所了解到的 CORS 是

跨源资源共享(CORS)是一种简单的、由浏览器和服务器实现的安全协议，它告诉哪些站点可以与哪些其他站点进行通信。

基本上，如果站点 A(您的站点)想要调用站点 B，它需要您的许可才能这样做。但是，它阻止了站点 B 在未经您允许的情况下调用站点 C，并对您的数据或站点造成可能的损害，或窃取信息，或任何其他各种可怕的事情。

一个很好的例子是一个来自第三方来源的广告(你允许)，这个第三方来源想要打电话给[www.stealallthispersonsdata.org](http://www.stealallthispersonsdata.org)(你不允许)。广告会显示，但数据不会被窃取。

也就是说，如果你正确设置了 CORS。

建立 CORS 需要一些基础的东西。我的看法是这样的:

## 我不完美的比喻

我妻子和我需要走出家门，让我们 10 岁的女儿来管理。

### 你为什么需要 CORS

我们没有给我们的女儿任何关于让谁进来以及他们能做什么的指示。陌生人敲门，女儿让陌生人进来。陌生人邀请更多的陌生人，抢劫我们的房子，给猫喷漆，窃取我们的个人信息。

CORS 给出了一个框架，规定了谁可以进来，谁可以给谁打电话。

### 设置访问-控制-允许-原点

我们告诉女儿，“除了帕蒂小姐(我们的邻居)，不要让任何人进来。”有人敲门，我们的女儿问是谁。然后，她可以根据我们告诉她让谁进来做出决定。

这允许你告诉浏览器你将允许哪些网站进入。

### 设置访问-控制-允许-凭证

我们告诉女儿，“除了帕蒂小姐，或者任何知道我们秘密暗号的人，不要让任何人进来。”

通常，CORS 不会在其请求中包含 cookies 或其他身份验证。将此项设置为 true 也会在请求中抛出 cookies。

### 设置访问控制允许方式

我们告诉女儿，“你可以邀请一个朋友过来，但是他们不能粉刷墙壁。”

这告诉 CORS 请求允许哪些请求方法:GET、POST、PUT、DELETE。所以，如果你想让一个网站做 GET，但不更新任何东西，你可以这样做。

### 设置访问控制最大年龄

我们告诉女儿，“你可以邀请一个朋友过来，但他们必须在下午 6 点离开。”

这张真的不完美。这将设置以秒为单位的值，以缓存预检请求结果，如 Access-Control-Allow-Headers 和 Access-Control-Allow-Methods 头中的数据。

### 还有更多！

CORS 是一个我刚刚开始理解的概念。帮我填空！这里少了什么？这个比喻有什么可以补充的吗？