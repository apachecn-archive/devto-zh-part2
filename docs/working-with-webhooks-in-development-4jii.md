# 在开发中使用 Webhooks

> 原文：<https://dev.to/alexdovzhanyn/working-with-webhooks-in-development-4jii>

我经常发现自己在构建应用程序或新功能时需要集成 API 或外部服务。我经常需要能够(几乎)实时获取信息。像 MailChimp 和 PayPal 这样的服务提供 webhooks，这基本上是一种描述外部 POST 请求到您的服务器的奇特方式([如果您不熟悉 HTTP 动词](http://www.w3schools.com/TAGS/ref_httpmethods.asp)，请单击此处)。任何在开发环境中使用过 webhooks 的人都知道，如果没有设置主机，使用它们是很痛苦的，因为不能将本地主机地址传递给这些服务。Localhost 差不多就是翻译成 127.0.0.1，这(通常)是每台计算机的主 IP 地址；告诉一个 API POST 到 localhost:3000/your_hook_path 字面意思就是告诉它 POST 到自己(而且通常 API 不会让你输入 localhost 作为 URL)。您可以看到这是如何在开发和测试过程中让您陷入尴尬境地的。

#### Ngrok:自切片面包以来最好吃的东西

Ngrok 是解决这个问题的一个非常好的工具，我个人非常喜欢这个工具(它不仅仅适用于 webhooks)。基本上，ngrok 设置一个 URL，将任何传入的请求路由到您选择的端口，这在使用 webhooks 时非常有用。设置 ngrok 也非常快。只需在这里下载它，cd 到您安装它的目录，并运行`./ngrok http 3000`，用您想要暴露的任何端口替换 3000。如果一切顺利(很可能会)，ngrok 将生成一个 HTTP 和 HTTPS URL，转发到您的本地主机。现在你所要做的就是为 API webhook 提供生成的 URL，瞧！在您的开发环境中已经有了一个工作的 webhook 监听器。值得注意的是，每次重启客户端时，ngrok 都会生成一组新的 URL，因此您需要在 APIs webhook 中交换出 URL。我也喜欢在开发过程中使用 ngrok 进行演示工作。这样，任何人都可以看到我正在开发的应用程序/网站的当前状态，而无需我设置主机，并且我可以避免部署，直到真正有必要的时候。它也非常适合在多种设备/浏览器上测试你的网站，因为你可以从几乎任何地方连接。

#### 人人都爱 Ngrok！

不要只相信我的话，这里有一些来自热爱 ngrok 的人的推文:

> "所以 ngrok.com 基本上是有史以来最伟大的事情."
> ——@菲拉达斯
> 
> " #ngrok 是用远程 API 测试本地主机的梦想！"
> ——@ davejlong
> 
> “#ngrok 是我用过的最简单的本地隧道解决方案。”
> —[@密码](https://dev.to/thecodeboss)

…实际上[有更多的爱](https://ngrok.com/love)。