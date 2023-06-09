# TightVNC 太多验证失败[已解决]

> 原文：<https://dev.to/juni/tightvnc-too-many-authentication-failures-resolved-7m5>

## 问题

[![vnc server the authentication error with too many authentication failures](img/c9b63819a7ddacfab059850a1fd18845.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BlVyNjdW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qdt7kcnqmrab1xjsbr60.png)

你想访问 ubuntu 服务器的图形用户界面，你安装 TightVNC 或 x2go。我在我的 Ubuntu 中安装了 TightVNC 的 vncserver，并能够访问它几天后突然看到了这个:“认证原因:认证失败次数过多”。那使我发疯，不让我进去。

问题可能是由于您尝试登录 vncserver 失败次数太多，或者是机器人使用暴力方法访问您的服务器。

## 解:

您必须终止 vnc 进程并重新启动它，才能再次访问 vncserver。通过 ssh 连接到您的服务器并运行以下命令

```
pgrep vnc

// the output will look like following but yours will be different (sure thing)
17732
23723 
```

Enter fullscreen mode Exit fullscreen mode

您将看到一个或多个针对 vncserver 运行的进程 id。只需为每个进程 id 运行以下命令

```
kill PID

// In my case I ran the following two since I had two process ids
kill 17732
kill 23723 
```

Enter fullscreen mode Exit fullscreen mode

现在你已经在你的 Ubuntu 上杀死了 TightVNC。再打开就可以重新连接了。要使 VNC 重新启动并运行，请运行以下命令。

```
vncserver -geometry 1920x1080 
```

Enter fullscreen mode Exit fullscreen mode

[![vnc server the solution to authentication error with too many authentication failures ](img/b3260a9c79ae2508f7e13644cec3b884.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MVqAIG2X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ia674uwp94n3ezsuonk6.png) 
就是这样！

TightVNC 在我的服务器上再次工作:
[![TightVnC connection made again after fixing authentication error with too many authentication failures](img/a1ad0891bd8b30a16654ad2dbf1229d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nvj4zJnk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m3tcps4s4p6lyg427enh.png) 
此外，关于如何防止这种情况再次发生的阅读可以在这里找到 [solveopti](https://solveoptimized.com/how-to-resolve-prevent-too-many-authentication-failures-with-vnc/) 。

您可以在 github 上帮助改进这篇文章(文件名称与文章标题相同)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)t67/[巨型，是](https://github.com/Juni4567/dev.to)

### 这是我在 dev.to 上发表的文章的储存库。你可以通过提交 PR 来贡献这些文章

<article class="markdown-body entry-content container-lg" itemprop="text">

# dev.to

这是我在 dev.to 上发表的文章的储存库。你可以通过提交 PR 来贡献这些文章

</article>

[View on GitHub](https://github.com/Juni4567/dev.to)