# 我日常使用的秘密工具

> 原文：<https://dev.to/kyleparisi/a-secret-tool-i-use-daily-4pek>

### 最好的工具是你不去想的

我使用的每台电脑和服务器都安装了 zerotier。什么是零层？这是虚拟网络，或基于软件的网络。我最初认为这个想法很好，但是要么 a .软件会让我失望，要么 b .公司会失败。我已经使用 zerotier 很多年了，这些情况都没有发生。

### 工作原理

Zerotier 使用 UDP 和 TCP 回退，并在您的计算机上创建虚拟网络设备。它通过零层根服务器建立网络，以获取每个网络的私有 IPv4 和 IPv6 地址。或者，你可以加入一个公共的零层网络，比如他们的“地球”网络，这个网络对任何人都开放。一旦连接，你得到加密的点对点连接。下面是从我的工作电脑跳到我的家庭服务器的样子:

[![traceroute](img/92036184c83072d534cf6e55eeb82ceb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PQuxMuJW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5q70dmqnzlveh1mid41q.png)

你可能认为我需要配置一百万个文件才能让它工作，就像一个 vpn 服务器一样。事实并非如此。

```
# after installing zerotier, using earth network ID here:
zerotier join 8056c2e21c000001 
```

一旦您加入，您可以登录到 zerotier 并允许这个新主机在网络上。

就是这样！之后就是直接魔法了。

### 那么我该如何使用它呢

我用它来登录我不同的电脑。很多时候，我会忘记在电脑上输入一些代码。我还可以维护我的家庭服务器，并根据我的零层 IP 地址限制某些网络接口。易用性完全消除了复杂的 VPN 设置的需要。我对 zerotier 印象深刻，我是 Alpine Linux zerotier 包的维护者。如果你喜欢一直保持联系，他们甚至有一个移动应用程序。

试试看。免费层非常合理。