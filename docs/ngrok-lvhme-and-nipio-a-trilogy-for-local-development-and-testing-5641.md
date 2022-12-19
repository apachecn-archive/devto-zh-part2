# ngrok、lvh.me 和 nip.io:本地开发和测试的三部曲

> 原文：<https://dev.to/nickjj/ngrok-lvhme-and-nipio-a-trilogy-for-local-development-and-testing-5641>

**本文最初发布于 2017 年 9 月 18 日:[https://nickjanetakis . com/blog/ngrok-lvh me-ni Pio-a-trilogy-for-local-development-and-testing](https://nickjanetakis.com/blog/ngrok-lvhme-nipio-a-trilogy-for-local-development-and-testing)**

* * *

 <small class="text-muted">**以上图片摘自 ngrok 网站。*</small> 

在开发真实世界的应用程序时，您可能会遇到某些障碍，例如:

*   如何在本地测试 webhooks？
*   我如何向客户展示我的网站的演示？
*   如何开发一个在 localhost 上使用子域的 web app？
*   没有域名怎么测试 Let's Encrypt？

在本文中，我们将使用 3 个免费工具和服务来涵盖所有 4 个用例。

## ngrok

Webhooks 是一个非常强大的概念。他们让你以一种非常简单的方式与第三方服务集成，你可以不需要不断地从服务中获取信息。

例如，假设您想在用 Stripe 创建发票后执行一个操作。你只需监听 Stripe 的`invoice.created`事件，当这发生在 Stripe 端时，它们会将发票的详细信息发送到你选择的网址。

如果您在生产环境中运行，并且有一个可公开访问的地址(如您的域名),这很好，但是在开发过程中呢？

你不能只告诉 Stripe 访问`http://localhost`，因为该地址对互联网是不公开的。

您可以通过在路由器上转发您的 web 服务器端口，然后使用您的公共 IP 地址作为 webhook URL 来解决这个问题，但这非常烦人，会暴露您的公共 IP 地址，并且它很可能会改变，因为大多数家庭级电缆/ DSL 连接都有动态 IP 地址。

不仅如此，有些服务要求你通过 HTTPS 来响应 webhooks，所以现在你也要负责设置 HTTPS，而实际上你想做的只是在开发中测试一个 webhook。

这就是 [ngrok](https://ngrok.com/) 发挥作用的地方。这是一个免费的工具，您可以下载并在您的 dev box 上运行。他们将自己描述为一个通往本地主机的安全通道，仅此而已。运行`ngrok http 3000`将端口 3000(或任何端口)转发到公共 ngrok 地址就这么简单。

##### 运行 ngrok http 3000 后，您会看到类似如下的内容:

```
Tunnel Status                 online                                            
Version                       2.0.25/2.1.18                                     
Region                        United States (us)                                
Web Interface                 http://127.0.0.1:4040                             
Forwarding                    http://19b31736.ngrok.io -> localhost:3000        
Forwarding                    https://19b31736.ngrok.io -> localhost:3000 
```

现在您可以访问`https://19b31736.ngrok.io`，它是运行在端口 3000 上的本地 web 服务的公共安全版本。不需要端口转发。

这不仅对 webhooks 有用，而且向客户展示一个演示站点也很棒。我一直都这么做。您只需启动 ngrok 隧道，与客户端进行实时通话，并引导他们浏览您的应用程序，而无需设置真正的主机。

## lvh.me

当开发使用子域名的网站时，这可能是开发中的一个噩梦，因为`localhost`不是一个完全合格的域名。不能直接去`example.localhost`。

幸运的是，现在在开发中使用子域非常容易。 [http://lvh.me](http://lvh.me) 是一项免费服务，它会将自身连同所有子域一起解析到`localhost`。

不需要安装或运行任何东西。事实上，如果你试图访问那个网站，它会失败，除非你碰巧有一个本地 web 应用程序运行在端口 80 上。

如果你运行`host lvh.me`，你可以看到它映射回`127.0.0.1`，也就是`localhost`。很简单。我曾经在[用 Let's Encrypt](https://nickjanetakis.com/blog/debugging-lets-encrypt-errors-sometimes-its-not-your-fault) 调试 DNS 错误的时候讲过这个`host`命令。

那么怎么用呢？

如果您正在构建一个多租户 web 应用程序，并且想要测试`example`租户，您所要做的就是转到`http://example.lvh.me`并且它会工作。搞定了。

它甚至可以在没有子域的情况下工作。例如，如果您的开发盒上有一个应用程序在`localhost:8000`运行，您可以转到`lvh.me:8000`来加载它。现在你可以用一个完全合格的域名来测试你的应用了。

## nip.io

最后，第一次设置 Let's Encrypt 可能相当困难。这就是为什么我制作了一个[端到端的视频课程](https://httpswithletsencrypt.com/?utm_source=devto&utm_medium=website&utm_campaign=ngrok-trio)，带你经历这一过程的每一步。

不管怎样，你最不想做的事情就是开始测试让我们对你的真实域名进行加密，因为如果你不小心的话，你很容易就能炸毁你的主站点。

此外，您不能将 SSL 证书直接注册到 IP 地址，因此您不能根据服务器的 IP 地址注册不同的证书。

现在...你可以在你的真实域名上使用一个伪子域，这是可行的(这也是我推荐的)，但是有时候你还没有达到你已经有了一个域名的地步。

然而，你可以通过使用[http://nip.io/](http://nip.io/)(以及类似的服务)来解决这个问题。

例如，让我们假设你有一个真正的网络服务器运行在 42.42.42.42 上。你可以访问 http://42.42.42.42.nip.io ,这将立即生效，无需安装或运行任何东西。您还可以针对该域测试您 Let's Encrypt 设置。

*注意:我将只使用 Let's Encrypt staging server 来测试这一点，因为所有 nip.io 地址将共享相同的证书创建实时速率限制，但这是测试您的设置所需的全部内容。*

#### 从技术上讲，nip.io 也适用于本地子域开发

你可以用 nip.io 完全取代 lvh.me，但是我觉得 lvh.me 更适合打字。如果你想继续使用 nip.io 的子域名，你可以访问 http://example . 127 . 0 . 0 . 1 . nip . io。对比一下*[http://example . lvh.me](http://example.lvh.me)*现在你知道我为什么更喜欢 lvh . me 了吧

这就是三部曲。我希望你喜欢它。我知道我喜欢几乎每天都使用这些工具！