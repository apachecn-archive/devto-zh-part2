# 以太坊节点怎么运行？

> 原文：<https://dev.to/ishwar/how-to-run-ethereum-node-7h4>

如何运行以太坊节点

在 ubuntu 服务器上运行以太坊节点要容易得多。

这篇文章提供了如何在 LTS Ubuntu 16.04 上安装快速以太坊网络的分步指导。

geth:

我们主要的以太坊 CLI 客户端。它是以太网(主网、测试网或专用网)的入口点，能够作为完整节点(默认)、归档节点(保留所有历史状态)或轻型节点(实时检索数据)运行。它可以被其他进程用作通过暴露在 HTTP、WebSocket 和/或 IPC 传输之上的 JSON RPC 端点进入以太网的网关。geth-help 和命令行选项的 CLI Wiki 页面。

以太坊:

以太坊是一个运行智能合同的分散平台，应用程序完全按照编程运行，没有停机、审查、欺诈或第三方干扰的可能性。

根据我的经验:

*   最低硬件要求(初始同步):4 个 CPU、8 GB RAM、100 GB 磁盘
*   最低硬件要求(正常操作):2 个 CPU、4 GB RAM、300 GB 磁盘

在同一个服务器上托管一个网站和创建以太坊节点不是一个好主意。

安装最新的以太坊软件。

从 PPA 安装

1.  安装 software-properties-common(add-apt-repository 命令位于软件包 software-properties-common 下，您需要安装此软件包才能安装 add-apt-repository)。

2.  使用 add-apt-repository 命令添加 ppa:以太坊/以太坊存储库。现在你要检查你的/sources.list.d/。例如，如果您正在将 Launchpad 添加到您的 wheety/sources . list . d/

3.  可以通过运行 sudo apt-get update(或 sudo aptitude update)来获得软件包的更新版本，建议在安装任何软件包之前运行它

4.  正在安装 Go 以太坊。从软件包管理器安装。通过自制程序在 macOS 上安装。通过 PPAs 安装在 Ubuntu 上。通过 Chocolatey 在 Windows 上安装。下载独立软件包。在 docker 容器中运行。从源代码构建它

如果你想留在出血边缘，安装以太坊-不稳定的软件包代替。安装后，运行 geth account new 在您的节点上创建一个帐户。
您现在应该能够运行 geth 并连接到网络。
确保使用 geth - help 检查不同的选项和命令

在一个 tmux 窗格中启动一个 light geth 节点。

照明
或
照明