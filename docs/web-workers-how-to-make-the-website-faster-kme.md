# 介绍 darkwash 库

> 原文：<https://dev.to/bsiscanu/web-workers-how-to-make-the-website-faster-kme>

**更新:Darkwasp 加入 Domy 项目(web 组件)。**

现在大多数网站都使用一个处理器内核，这样做极大地降低了用户体验，冻结了整个网络的实际进度。在深度学习和实时体验的时代，web 似乎碰壁了。

为什么会发生这一切？原因非常简单——线程安全。Web Worker 被设计成隔离的，最不方便的是从一个单独的文件中运行 Web Worker 的约束。

因此，为了促进 web 环境中并行进程的工作，我们推出了名为 Dark Wasp 的新库。简而言之，这种想法是将 web 应用程序代码的最大部分保存在存储中，并在单独的线程中按需加载，而无需一次性加载整个代码库。相同的功能可以用在前端和后端(web 浏览器或 Node.js)。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [团队](https://github.com/teamdomy) / [黄蜂](https://github.com/teamdomy/wasp)

### Web Workers 超集

<article class="markdown-body entry-content container-lg" itemprop="text">

# 函数的分布式存储

该软件包简化了并行进程的工作(多核处理器上的网站)。它支持网络浏览器和后端客户端。

[Apache 2.0 许可证](https://www.apache.org/licenses/LICENSE-2.0)

### 启动第一个应用程序

为了开始工作，调用`connect`函数，它返回`Promise`并监听具有 2 个属性的对象:

1.  `app` -您的应用程序/存储的名称，以及
2.  `peer` -用户的秘密且唯一的`UID`(把它想象成一个字符串中的用户名和密码)。

该软件包提供了 3 种内核类型，分别命名为`agents` : `Peer`、`Wasp`和`Swarm`。所有的`agent`方法都返回`Promise`。

出于示例的目的，让我们创建并保存两个带有函数的文件，我们稍后将存储它们:

*random.js*

```
module.exports = function() {
  return (Math.random() + 10).toString(7)
}
```

Enter fullscreen mode Exit fullscreen mode

*fibonacci.js*

```
module.
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/teamdomy/wasp)

要访问存储，我们需要存储本身的名称-“app”属性和当前用户的唯一标识符-“peer”属性。如果给定名称的存储不存在，它将被创建，用户将获得所有者权限。最新加入的用户将具有有限的权利，是简单的对等用户。

现在，为了举例，让我们创建一个随机函数: