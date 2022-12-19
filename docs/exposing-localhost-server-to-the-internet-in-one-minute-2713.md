# 在一分钟内将本地主机服务器暴露给互联网

> 原文：<https://dev.to/levivm/exposing-localhost-server-to-the-internet-in-one-minute-2713>

有时，由于多种原因，我们会发现这样的情况，我们需要将本地服务器暴露在互联网上，可能我们需要向客户展示我们的网站，但没有足够的时间进行部署，其他情况下，我们需要从第三方服务中测试 webhook，我们不想只为了测试而创建新的部署。

所以，让我们努力吧。

## ngrok

有些服务让你在他们的服务器和你的本地机器之间建立一个隧道。有好几个，不过我的推荐是`ngrok`。

它有一个免费增值模式，它符合最基本的要求。

### 工作原理

我们下载并运行一个程序( **ngrok** )，指定本地服务器监听哪个端口。 **ngrok** 将连接到他们的外部云服务，创建一个公共端点，将所有流量从它重定向到你的服务器。就这样，简单。

### 下载和设置

1.  从[官方网站](https://ngrok.com/download)下载
2.  将`/path/to/ngrok.zip`解压到任意文件夹
3.  在您想要的任何端口启动 HTTP 隧道。让我们在 80 上做它

```
$ ./ngrok http 80 # this command should be run from where ngrok was unzipped 
```

Enter fullscreen mode Exit fullscreen mode

```
...........
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://82330e5b.ngrok.io -> localhost:80
Forwarding                    https://82330e5b.ngrok.io -> localhost:80
........... 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。我们只需要在端口 80 运行我们的 web 服务器，并开始接收流量。

如果你想测试一下。您可以使用 curl 向我们的公共 ngrok 域发送请求，或者在浏览器中打开它。此外，ngrok 允许`https`连接，当我们测试需要安全连接的外部服务时，这是一个很好的特性。

注意:记住您需要让您的本地服务器在端口 80 上运行(对于这个例子)。

```
$ curl -v http://82330e5b.ngrok.io 
```

Enter fullscreen mode Exit fullscreen mode

您可以在`http://localhost:4040`使用内置的 ngrok 仪表板查看所有请求的详细信息

[![Dashboard](../Images/611d55e50c6059aa21985740ceff2527.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x5JXJNiz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iesb6jqsmab4q5z1sor4.png)

仅此而已。对于一些人来说，这可能看起来像一个微不足道的任务，但对于初学者来说，这是一个起点。

如果你喜欢它，传播这个词。

披露:这是为了测试的目的，如果你正在处理敏感数据，避免使用这种方法，因为你所有的流量将通过第三方服务。