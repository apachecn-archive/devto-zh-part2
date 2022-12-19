# SSH 端口转发如何使用 SSH 进行端口转发

> 原文：<https://dev.to/samuyi/the-how-to-of-ssh-port-forwarding-1f4e>

[![Port Forwarding vs Direct communication](../Images/871c5397cdc21ccb60548e81ab47e195.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y_5i62YS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qpawdpvr1du4fwbknq50.gif) 
端口转发是两个应用程序之间的一种交互，通常是 TCP/IP 应用程序，它们使用 SSH 连接相互通信。SSH 拦截来自主机上的客户端应用程序的服务请求，创建一个 SSH 会话，将请求传送到 SSH 连接的另一端。在将请求发送到远程主机上的应用服务器之前，另一端对请求进行解密。端口转发可用于保护传统上不安全的应用程序之间的通信。它们还可以用于不可能的通信，例如 IT 管理员通过防火墙阻止外部访问主机上的某些端口以提高安全性，通过端口转发，可以访问远程计算机上运行的应用程序。在之前的[帖子](https://dev.to/samuyi/ssh-agents-in-depth-4116)中，我们讨论了一种不同类型的转发，叫做 ssh-代理转发。这使我们能够使用公钥认证创建从一台计算机通过一台远程主机到第三台远程主机的 SSH 连接，而不需要在第二台远程主机上拥有您的私钥。端口转发有时被称为“隧道”，因为它提供了一种通过 SSH 保护 TCP/IP 连接的方法。

[![local port forwarding](../Images/462e237860fbbe7d4d93c4ebca7be5b7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X7qVeNxx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/la8u765h1lxutfmvonsx.gif)

## 本地端口转发

假设您有一个运行在远程主机上的 IMAP 服务器，并且您想在您的家庭机器上使用电子邮件客户端来访问该服务器。还假设远程主机的管理员非常偏执，他们会阻止除 80、433 和 22 端口之外的所有外部访问。不幸的是，由于 IMAP 运行在 143 端口，你不能从家里访问它。您需要做的就是使用 SSH 通过隧道连接到 IMAP 服务器。您需要在本地机器上运行的命令是:

```
$ ssh -L2001:localhost:143  remote.net 
```

让我们分解上面的命令。-L 开关指定本地转发；这实际上意味着 TCP 客户端(您的电子邮件客户端)位于您的本地机器上。2001 代表您希望电子邮件客户端连接到的本地计算机上的端口。本地主机意味着服务器上连接的源套接字似乎来自本地主机。这意味着上面的命令可以写成:

```
$ ssh -L2001:remote.net:143 remote.net 
```

源数据包看起来来自 remote.net 地址。这在某些 TCP 应用程序中可能微不足道，但有些服务器被配置为进行访问控制并阻止来自环回地址的连接。或者它可能运行在多宿主主机上，并且只绑定了主机拥有的地址的子集，可能不包括环回地址。通常最好使用第一个命令。最后，143 代表 IMAP 服务器在远程服务器上运行的端口号。

默认情况下，SSH 客户端只监听来自本地机器的连接。也就是说，它只接受来自在您的机器上本地运行的应用程序的连接。默认情况下，外部应用程序试图通过网络连接到您的 SSH 客户端的任何尝试都将失败。要做到这一点，你需要像这样调整上面的命令:

```
$ ssh  -g -L2001:localhost:143  remote.net 
```

-g 开关表示客户端配置文件中的 GateWayPorts 选项；如果将其设置为 yes，则不需要-g 选项。

[![remote port forwarding with ssh](../Images/f69f4ea0a3ad74c856ed204e2e069ffe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6ht2e_RB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xl0u5wy98adbgna4zr2a.gif)

## 远程端口转发

远程转发的一个例子是，假设我们有一个运行在远程服务器 shell.isp.net 上的电子邮件客户端，我们想访问另一个安装了 SSH 服务器的远程服务器 remote.host.net 上的 IMAP 服务器，那么远程转发就是这种情况下的解决方法。建立远程转发的命令是:

```
$ ssh -R2001:localhost:143  remote.host.net 
```

语法类似于本地端口转发，唯一的区别是本地端口转发中的-R 开关代替了-L 开关。

远程转发和本地转发有细微的区别。主要区别在于，在本地转发中，SSH 客户端监听来自应用程序客户端的通信，因此通常驻留在与应用程序客户端相同的机器上。在远程转发中，SSH 服务器监听来自应用程序客户机的通信，SSH 服务器和应用程序客户机驻留在同一个主机上。

## 动态端口转发

您可能会问，是否有可能在端口 80 上建立 HTTP 连接隧道，以使用本地转发访问不安全的网站？答案是肯定的，但随之而来的是许多限制。例如，由于浏览器仅在本地主机上运行，因此跟随绝对 URL 如"[http://insure/web/now . html "](http://inseure/web/now.html%E2%80%9D)将不起作用，因为浏览器仅知道本地主机。尝试用您的浏览器代理请求将不起作用于除端口 80 之外的任何连接。在这种情况下，对我们有用的是动态端口转发。 [SOCKS](https://en.wikipedia.org/wiki/SOCKS) 是一种动态转发协议，它被流行的浏览器如 [tor](https://www.torproject.org/) 用来代理请求和通过隧道到达某些国家审查的网站，并保护用户的隐私。SOCKS 客户端通过 TCP 连接，并通过协议指示它想要到达的远程套接字；SOCKS 服务器建立连接，然后退出，透明地来回传递数据。使用 SSH 实现这一点的命令是这样运行的:

```
$ ssh -D 1080 remote.host.net 
```

当用户在浏览器中键入绝对 URL 时，包括任何端口，例如“[http://myweb:1890”](http://myweb:1890%E2%80%9D)。浏览器连接到端口 1080 上的 SSH socks 代理，并请求连接到 myweb:1890。SSH 客户端将浏览器的连接与一个新的 SSH 会话相关联，然后连接到 SSH 服务器。SSH 客户端和服务器基本上是分开的，浏览器直接连接到 web 服务器。SSH 会为每个到不同网站的新连接分配一个新的套接字。

## 结论

端口转发是一种通用的 TCP 代理功能，它通过 SSH 会话建立 TCP 连接。它对于保护可能不安全的协议和可能被防火墙阻止的 TCP 连接很有用。然而，有一些 TCP 协议可能无法与端口转发(如 FTP)一起正常工作。认证之后，FTP 会在客户端上打开随机端口。这使得端口转发变得不必要的复杂。总的来说，大多数 TCP 协议都使用端口转发。