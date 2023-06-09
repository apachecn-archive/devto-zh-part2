# 通过 Snap 在 Linux 上安装 Linode CLI

> 原文：<https://dev.to/felicianotech/install-the-linode-cli-on-linux-via-snap-4o5m>

如果你运行一个 Linux 发行版(distro ),你想安装全新的 Linode CLI，这里有一个简单安全的方法，通过一个 snap 包。

回到 2017 年，Linode [宣布了他们新的 v4 API](https://www.linode.com/community/questions/11221/linode-rest-api-v4-early-access) 。
这个新的 API(我相信还在开发中)让旧的相形见绌。
它比老款更加“宁静”,并支持更多老款没有的功能。

在新 API 的基础上，开发了新的 Linode 开发者网站 T1，在 https://cloud.linode.com 的 T4 提供了新的 Linode 管理器预览 T3，当然，还有新的 Linode CLI。

新的 Linode CLI，用 Python 写的，很有用。
通过 [Linode Docs](https://www.linode.com/docs/platform/api/using-the-linode-cli/) 详细介绍其功能和使用方法。
虽然它可以通过`pip`安装，但是如果您还没有安装`pip`或者想要一种更安全的方式来运行 CLI，您可以通过 snap 安装它。

您可以通过 snap 为 Ubuntu 16.04、Ubuntu 18.04+、elementary OS 5、Debian 9+、Fedora 28+等安装`linode-cli`:

```
sudo snap install linode-cli 
```

与所有快照一样，它将自动更新并与您系统的其余部分隔离，这意味着安全。
关于为什么应该使用快照的更多信息[请点击这里](https://www.fossmint.com/what-are-ubuntu-snaps-and-how-are-they-important/)。

这个快照的源代码可以在[这里](https://github.com/felicianotech/linode-cli-snap)找到，而 Linode 的 CLI 本身的源代码可以在[这里](https://github.com/linode/linode-cli)找到。