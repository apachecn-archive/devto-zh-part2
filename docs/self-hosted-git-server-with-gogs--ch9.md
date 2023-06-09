# 带有 Gogs 的自托管 Git 服务器

> 原文：<https://dev.to/zorexsalvo/self-hosted-git-server-with-gogs--ch9>

### 什么是 Gogs？

Gogs (gogs.io)是一个无痛的自托管 Git 服务。

如果你想拥有自己的类似 GitHub 的服务，这是我最好的选择，因为我在不到一个小时内就让它工作了。也许你会想，既然已经有了 GitHub，为什么还会有人想要创建另一个像 GitHub 这样的服务呢？对我来说，我需要为我的一些项目建立私人仓库。我不能在 Github 中做到这一点，除非我以每月 7 美元的价格将我的帐户升级为“开发者”计划。老实说，对我来说，支付 7 美元的私人储存库已经很贵了，或者我真的很穷。我没有以任何方式描述 GitHub，对于公开的回复，我仍然会选择 GitHub。

### 重要的事情先来

你需要:至少有 docker 的基本知识，当然你需要在你的机器上安装 docker。PS，GOGS 安装不必总是 dockered，也有裸机安装指南，但只是为了这篇文章和我的资源，我们将坚持 docker 版本，你也需要一个 VPS，它可以在云中访问。

当你完成了第一步，只需拉官方 gogs docker 图像:
`$ docker pull gogs/gogs`

您还必须创建一个 db (mysql、postgres 等)容器，因此:
`$ docker pull postgres`

一旦 db 准备就绪，您就可以运行 gogs-container 了:

```
$ docker run --name gogs -d -v /var/gogs/:/data gogs/gogs 
```

在那之后，一旦你打开地址，你将看到 Web UI 服务器配置，在那里你将键入所有的服务器细节，数据库，应用程序名称，管理员，主机，域等。一旦你完成了所有这些，你现在就可以走了。

**供大家参考:**

*   我将它(连同这个个人网站和其他个人应用程序)托管在 vps 上(1GB RAM，1CPU 内核，20GB SSD 存储，1TB 传输，1000Mbps 网络输出中的 40Gbps 网络),每月 5 美元；我以不到 1 美元的价格从 Godaddy 那里购买了我的域名；我使用 nginx 进行反向代理。

对于个人 git 服务器，Gogs 确实是我的随身服务器。我在安装它的时候没有遇到任何问题，它拥有你正在寻找的 g it 服务器的基本功能，而且它非常轻量级！