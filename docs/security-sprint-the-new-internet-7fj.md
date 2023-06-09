# 安全冲刺:新互联网

> 原文：<https://dev.to/terceranexus6/security-sprint-the-new-internet-7fj>

又见面了！欢迎来到每周一次的安全冲刺赛

今天我要介绍一件非常激动人心的事情。但在此之前，让我们暂时回到过去。我们是在 80 年代后期，互联网是非常令人兴奋的，每个人都想有一个网页...交流已经改变，我们正在进入一种新的生活方式，在这种生活方式中，我们可以匿名，用户之间没有墙，知识是开放的。但是...啊哦，有些国家对这种说法不太满意...互联网开始变得如此重要，它塑造了社会。如果你身处一个雄心勃勃的公司或国家，你会怎么想...谁控制了互联网，谁就拥有了社会影响力，对吗？

一步一步地，我们所知道的互联网现在正被限制、审查和监控。为了防止和打击这一现象，有一些协会、活动家等等...其中一个提议是...IPFS 议定书。

正如官方网站描述的那样，IPFS 是“*点对点超媒体协议
使网络更快、更安全、更开放的*”。它想提供一个分布式的替代协议，使它更有弹性，这对于发展中国家来说很方便。这是来自官方网页的信息图:

[![](img/710fa227e3ab395706ed3e14cc54a357.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---pG_EJOY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lqh1tsxch21kwlbjfz4r.png)

[安装可以在不同的操作系统中运行](https://docs.ipfs.io/introduction/install/)，但我将重点放在 Linux 上。文档很清楚，并且有安装它的脚本。安装完成后，我们应该创建一个存储库来存储数据/设置。为此，我们将使用:

```
$ ipfs init
initializing ipfs node at /Users/jbenet/.go-ipfs
generating 2048-bit RSA keypair...done
... 
```

Enter fullscreen mode Exit fullscreen mode

我们的节点 ID 是`peer identity`散列。如果我们成功了，我们应该能够按照回购安装中的提示开始:

```
ipfs cat /ipfs/ourhash/readme 
```

Enter fullscreen mode Exit fullscreen mode

记得用我们的`peer identity`散列来改变`ourhash`。一旦我们按照自述文件中的说明完成了基本设置，我们就可以上线了...

```
$ ipfs daemon
Initializing daemon...
... 
```

Enter fullscreen mode Exit fullscreen mode

耶！我们在网络中...您可以使用`ipfs swarm peers`命令查看 ipfs 地址或您的对等项。还有一个 web 界面控制台，我们可以通过链接[http://localhost:5001/ipfs/your hash](http://localhost:5001/ipfs/yourhash)访问。

[![](img/46da768145bd3ef86daa8c1a3e7e4298.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eeDFztMl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2wi2a4s14ak07lmqdzq4.png)

我们可以在“文件”中添加内容。

[![](img/6b033192234fad4ab41bdc3041c2cf17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W3SYFzFA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pfjnxe7pe0v8dn8c1djc.png)

IPFS 的安全仍然很薄弱，因为他们还在声称要接受审计。这是一个调查分布式技术的好机会！

希望你们喜欢这个对 IPFS 的介绍，我很想尽快再写一次，更深入的介绍。