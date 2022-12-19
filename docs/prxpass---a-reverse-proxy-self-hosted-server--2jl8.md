# prx pass——一个反向代理自托管解决方案(又名本地隧道)

> 原文：<https://dev.to/defman/prxpass---a-reverse-proxy-self-hosted-server--2jl8>

我终于度过了倦怠期，开始编写不同的代码。我需要一个反向代理服务器，它像 ngrok 或 localtunnel 一样工作，但却是自托管的(我知道 localtunnel 是自托管的，但它是在 JavaScript 中...)所以我自己写了。

这个想法很简单:您只需在您的服务器上启动`prxpass-server`，然后在您的本地机器上，您启动`prxpass-client`，它连接到服务器，并将对`<your_id>.<hostname>`的每个请求代理到您的本地 web 应用程序(例如`localhost:4000`)。

例如，如果你正在开发一个使用 webhooks 的机器人(比如一个 Telegram 机器人)，你可以告诉 Telegram 发送东西给例如`mysuperduperbot.prxpass.defman.me`(如果服务器实例允许，你可以设置自定义 id)，prxpass 会将每个请求代理给例如`localhost:9000`。在你的 bot 中，你只需要在`localhost:9000`监听 POST 请求，在这种情况下，一旦 LetsEncrypt 推出通配符证书，你甚至可以设置一个 HTTPS。

还有其他使用 prxpass 的方法，例如，如果您想向某人现场演示您的 web 应用程序，而不将其上传到服务器。

prxpass 并不局限于 HTTP，而是 TCP atm(计划在将来添加 UDP 代理)，所以在 idea 中，您可以代理通过 TCP 栈工作的任何东西。不过，我还没有测试过。

如果你对我如何改进我的程序有任何想法——请在评论中写下你的想法，或者在 GitHub 上发表一个问题。如果你有兴趣使用它(它是 alpha ATM)——请告诉我，我会为你构建二进制文件。(除了 windows 客户端和 linux 服务器，我没有费心构建任何东西)。

链接:

*   [prx pass-服务器](////github.com/Defman21/prxpass-server)
*   [prx pass-客户端](////github.com/Defman21/prxpass-client)