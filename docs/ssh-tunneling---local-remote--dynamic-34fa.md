# 动态 Ssh SSH 隧道-本地、远程和动态

> 原文：<https://dev.to/__namc/ssh-tunneling---local-remote--dynamic-34fa>

原始文章发表于: [namc.in - SSH 隧道](https://namc.in/2018-06-26-ssh-port-forwarding)

我们大多数人都熟悉 SSH(Secure Shell)——一种允许我们安全登录远程系统的协议。SSH 隧道(也称为 SSH 端口转发)是 SSH 的一项功能，它在本地和远程系统之间转发加密连接。SSH 隧道使用已经建立的 SSH 连接来发送额外的网络流量。

我们将了解三种类型的端口转发——本地、远程和动态。

## 本地端口转发

让我们看看 SSH 的手册页告诉我们关于`local`
的内容

```
local: -L 
Specifies that the given port on the local (client) host is to be forwarded to the given host and port on the remote side. 
```

Enter fullscreen mode Exit fullscreen mode

因此，一般来说，该命令看起来类似于

```
$ ssh -L sourcePort:forwardToHost:destPort connectToHost 
```

Enter fullscreen mode Exit fullscreen mode

这翻译过来就是——用 ssh 连接到`connectToHost`，并将所有到**本地** `sourcePort`的连接尝试转发到名为`forwardToHost`的机器上的端口`destPort`，该端口可以从`connectToHost`机器到达。也可以使用 Unix 套接字进行转发。

假设 YouTube 在你的办公网络上被屏蔽了，你真的很想看一些猫咪视频。所以，你可以通过创建一个不在你网络上的服务器的隧道来访问 YouTube。上面的命令可以翻译成:

```
$ ssh -L 9000:youtube.com:80 user@example.com 
```

Enter fullscreen mode Exit fullscreen mode

这里的`-L`标志表示我们正在进行本地端口转发。我们与`user@example.com`机器连接。然后，我们将本地机器上端口 9000 的任何连接转发到`youtube.com`上的端口 80(这是 HTTP 的默认端口)。现在，如果您打开浏览器并转到`http://localhost:9000`，就会向监听`youtube.com`的 HTTP 服务器发出一个请求。但是，您在本地机器上没有运行 web 服务器。

您仍然无法访问 see 主页，所以不必担心。

浏览器中对`localhost:9000`的请求是用 localhost 值的主机目标头构建的。该请求到达`youtube.com`机器。但是这个请求被忽略，并显示一条错误消息- `Invalid virtual host ..`，因为 localhost 不能是运行`youtube`的服务器上的域名。

为了解决这个问题，我们更改了主机 HTTP 头，使远程 web 服务器能够识别相应的目的地。

```
$ curl -H "Host: youtube.com" -L localhost:9000 
```

Enter fullscreen mode Exit fullscreen mode

curl 中的参数用于跟踪重定向。只要你连接到`user@example.com`机器，你现在应该可以在`localhost:9000`上看到`youtube.com`主页的内容。

还有！SSH 隧道的好处是它们是加密的，所以没人能看到你在访问什么网站——只有 SSH 连接到你的服务器。(接招吧，办公室管理员！！😎)

默认情况下，任何人(即使在不同的机器上)都可以连接到本地机器上的指定端口。通过提供一个绑定地址:
,这可以被限制为同一主机上的程序

```
$ ssh -L 127.0.0.1:9000:youtube.com:80 user@example.com 
```

Enter fullscreen mode Exit fullscreen mode

SSH 绑定到本地机器上的端口 9000。任何到达这个端口的流量都被发送到监听远程机器`user@example.com`的 SSH 服务器。一旦被远程机器接收，流量就被发送到 127.0.0.1 的端口 80，也就是`user@example.com`本身。

### 连接防火墙后的数据库

`forwardToHost`主机也可以指通过其建立 ssh 连接的远程机器(即`connectToHost`)——在这种情况下，`forwardToHost`的值变为 127.0.0.1 或 localhost，作为已经与`connectToHost`建立连接的上下文中的本地主机。

```
$ ssh -L 9000:127.0.0.1:80 user@example.com 
```

Enter fullscreen mode Exit fullscreen mode

例如，当您需要连接到数据库控制台时，出于安全原因，该控制台只允许本地连接，在您的服务器上运行 PostgreSQL，默认情况下，服务器监听端口 5432。

```
$ ssh -L 9000:localhost:5432 user@example.com 
```

Enter fullscreen mode Exit fullscreen mode

该命令将本地端口 9000 转发到远程机器上的端口 5432。您可以在 localhost:9000 上使用 psql 通过本地机器连接到远程 PostgreSQL 服务器，就像:

```
$ psql -h localhost -p 9000 
```

Enter fullscreen mode Exit fullscreen mode

让我们花一点时间来理解实际发生了什么。

在 YouTube 的例子中，`9000:youtube.com:80`表示将我的本地端口 9000 转发到端口 80 的`youtube.com`。所以服务器上的 SSH 实际上在这两个端口之间建立了一个隧道(连接)——其中一个位于本地机器上，另一个位于目标机器上。

在数据库连接的例子中，`9000:localhost:5432`表示从服务器的角度来看的本地主机，而不是您机器上的本地主机。换句话说——将我的本地端口 9000 转发到服务器上的端口 5432——因为当你在服务器上时，本地主机意味着服务器本身。

小于 1024 或大于 49151 的端口号是为系统保留的，只能由 root 转发。如果您使用任何类型的端口转发，您需要指定目的服务器，即`connectToHost`。

默认情况下，启用端口转发。如果不是，检查`/etc/ssh/sshd_config`中的`AllowTcpForwarding`。

## 远程端口转发

再次回到手册页查看`remote`
的定义

```
remote: -R 
Specifies that the given port on the remote (server) host is to be forwarded to the given host and port on the local side. 
```

Enter fullscreen mode Exit fullscreen mode

该命令看起来很像本地隧道命令，但是带有一个`-R`标志。

```
$ ssh -R sourcePort:forwardToHost:destPort connectToHost 
```

Enter fullscreen mode Exit fullscreen mode

这翻译过来就是——用 ssh 连接到`connectToHost`，并将所有连接到**远程** `sourcePort`的尝试转发到名为`forwardToHost`的机器上的端口`destPort`，该端口可以从`connectToHost`机器到达。也可以使用 Unix 套接字进行转发。

好吧！让我们看一个例子。

假设你正在本地机器上开发一个应用程序，你想把原型展示给你的老板。

在大多数情况下，ISP 不会为您提供公共 IP 地址，因此您不能通过互联网直接连接您的机器。虽然这个问题可以通过在路由器上配置 NAT(网络地址转换)来解决——这可能并不总是有效，但是改变路由器的配置会产生技术开销，并且您需要对网络进行管理访问。

在这种情况下，您可以在互联网上设置一个可以公开访问的服务器，并且可以使用 SSH 访问。然后我们告诉 SSH 创建一个隧道，在服务器上打开一个新端口，您通过您机器上的本地端口连接到它。

```
$ ssh -R 9000:localhost:3000 user@example.com 
```

Enter fullscreen mode Exit fullscreen mode

这里的语法非常类似于本地端口转发，只对 **-R** 做了一个简单的修改 **-L** 。

SSH 将连接到远程机器——在本例中是`user@example.com`。标志 **-R** 让 ssh 监听那台机器的端口 9000。一旦机器上有一个进程连接到 9000，在同一台机器上侦听的 ssh 服务器就会将该连接转移到本地机器上——启动 ssh 通信的机器——并将其转发到端口 3000 上的 localhost。

远程端口转发允许通过 SSH 将本地机器的端口映射到远程服务器。

你需要做的另一件事是设置`GatewayPorts`。在你的`/etc/ssh/sshd_config`中，设置

```
GatewayPorts yes 
```

Enter fullscreen mode Exit fullscreen mode

并重启 SSH

```
$ sudo service ssh restart 
```

Enter fullscreen mode Exit fullscreen mode

这允许 SSH 服务器将端口 3000 绑定到通配符地址——这样端口就可以用于`connectToHost`远程机器的公共地址。

您还可以将`GatewayPorts`设置为`clientspecified`——在这种情况下，远程端口不会绑定通配符地址。您可能需要显式地指定一个空的绑定地址来绑定通配符地址——这可以通过在远程端口前面加上`:`符号来实现。

```
$ ssh -R :9000:localhost:3000 user@example.com 
```

Enter fullscreen mode Exit fullscreen mode

您还可以指定允许连接到端口的 IP 地址，以便只允许从给定 IP 地址到给定端口的连接。

```
$ ssh -R 1.2.3.4:9000:localhost:3000 user@example.com 
```

Enter fullscreen mode Exit fullscreen mode

现在，您的老板将能够通过将浏览器指向端口 9000 上的`connectToHost` IP 地址来访问端口 3000 上的应用程序。

### 双转发

如果远程服务器已经将`GatewayPorts`设置为`no`，并且无法更改，您可以执行上面的远程转发，然后使用`-g`选项从远程服务器执行本地转发。

```
$ ssh -g -L 9000:localhost:3000 user@example.com 
```

Enter fullscreen mode Exit fullscreen mode

**-g** 允许远程主机连接到本地转发端口，这将使服务器上的环回端口 3000 可在端口 9000 上的所有接口上访问。

## 动态端口转发- SOCKS

动态端口转发允许跨一系列端口进行通信。该端口转发是使用 **-D** 参数创建的。这使得 SSH 充当了 [SOCKS 代理服务器](https://en.wikipedia.org/wiki/SOCKS)。

有两种 SOCKS 协议——SOCKS 4 和 SOCKS5。这些基本上是使用代理服务器在服务器和客户端之间路由数据包的互联网协议。SOCKS5 同时使用 TCP 和 UDP，而 SOCKS4 只使用 TCP。

SOCKS 代理是一个简单的 SSH 隧道，其中特定的应用程序通过隧道将它们的流量转发到远程服务器，然后代理将流量转发到一般的互联网。与 VPN 不同，SOCKS 代理必须在客户机上为每个应用程序单独配置。

动态端口转发可以处理来自多个端口的连接。它分析流量以确定给定连接的目的地。但是，您可能需要配置程序来使用 SOCKS 代理服务器。

这可以通过
来完成

```
$ ssh -D 9000 -f -C -q -N connectToHost 
```

Enter fullscreen mode Exit fullscreen mode

**-D** 告诉 SSH 在端口 9000 上创建一个 SOCKS 隧道。
**-f** 将进程分叉到后台。
**-C** 在发送之前压缩数据。
**-q** 启用安静模式。
**-N** 告诉 SSH，一旦隧道建立，将不会发送任何命令。

使用代理的一个缺点是性能下降和错误标记的错误，因为它们重写了数据包报头。使用 SOCKS5 代理，服务器不会重写数据包报头——因此性能更高，不容易出现数据路由错误。因为 SOCK5 代理它们是底层的，所以它们可以处理任何类型的数据流量——程序、协议等。

## 关闭提示！

`-nNT`标志将导致 SSH 不分配 tty，只进行端口转发。这将防止每次创建隧道时创建外壳。

```
$ ssh -nNT -L 9000:youtube.com:80 user@example.com 
```

Enter fullscreen mode Exit fullscreen mode

### 更多阅读

*   [SSH 隧道](https://blog.trackets.com/2014/05/17/ssh-tunnel-local-and-remote-port-forwarding-explained-with-examples.html?utm_source=cronweekly.com)
*   [UnixSE-ssh 隧道如何工作](https://unix.stackexchange.com/questions/46235/how-does-reverse-ssh-tunneling-work)
*   [UnixSE - ssh 端口转发从任何地方访问我的家用机器](https://unix.stackexchange.com/a/19624)
*   [宋承宪/宋承宪的黑魔法可以做到？](https://vimeo.com/54505525)——这是一段视频。