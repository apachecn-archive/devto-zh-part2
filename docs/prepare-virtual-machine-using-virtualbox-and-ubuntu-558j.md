# 使用 Virtualbox 和 Ubuntu 准备虚拟机

> 原文：<https://dev.to/pongsatt/prepare-virtual-machine-using-virtualbox-and-ubuntu-558j>

这篇文章将指导你创建 4 个基于 Ubuntu 服务器的虚拟机。一个被指定为主节点，另外三个被指定为工作节点。

这是[在虚拟机](https://dev.to/pongsatt/setup-your-own-kubernetes-cluster-on-vms-5ln)系列上设置您自己的 kubernetes 集群的一部分。

**注:**

> 本指南基于 Windows，但由于它是一个虚拟机，其他操作系统应该没有什么不同

## 先决条件:

*   安装 [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
*   为 VirtualBox 或[64 位直接链接](https://download.virtualbox.org/virtualbox/5.2.20/virtualbox-5.2_5.2.20-125813~Ubuntu~xenial_amd64.deb)下载 [Ubuntu 16.04 镜像](https://www.virtualbox.org/wiki/Linux_Downloads)
*   每个虚拟机可用 2GB 内存(4 个节点需要 8GB)。根据可用的 RAM，您可以减少节点数量
*   每个虚拟机 10-30GB 的磁盘空间(4 个节点大约需要 100GB)

## 最后

你会得到这样的东西。

[![At the end](img/764b117b8fe397df606e7903323e4bb0.png "In the end")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dek-Q6Z_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o4r7iptl0xos4cxizdgi.png)

# 1。创建虚拟机

我们将创建 4 台虚拟机。1 台机器用于 kubernetes 主节点，复位用于 worker 节点。除了主机名，它们几乎都是一样的。RAM 和 cpu 可以根据您的需要进行调整。理论上，主节点比工作节点需要更少的资源。

### 1.1。创建新的虚拟机

*名称:*k8s master
T3】类型: linux
*内存:* 2048MB

[![Step1](img/eb48a7016b21c07e0ba11c695091e316.png "Create1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lx0RPsTa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ms7949bf014nnmhx6mbk.png)

### 1.2。选择硬盘大小

*大小:* 10GB

[![Step2](img/468952379af18e96a9dc2b4525860e59.png "Create2")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zteOsCsE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/37eypv0vb11ns294eqei.png)

[![Done](img/0e34fa41e8a866a88aae648e9cce3830.png "Create3")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ocju_F73--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/09wude0rg0qg58hg9iu9.png)

# 2。安装操作系统前准备虚拟机

### 2.1。设置网络

我们将使用桥接网络，这样我们就有了可以与外界通信的真实网络 ip 地址。

*   单击“设置”，然后单击“网络”。
*   选择“连接到”作为“桥接适配器”
*   点击“前进”
*   记下“MAC 地址”以保留 IP 地址，这样您的 IP 地址就不会在每次虚拟机重启时发生变化

[![](img/25dcf4e3dead35a9b04dbd99238f51f8.png "Network setup")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g2ReuJKH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m5nxilmrzyber68rftzj.png)

### 2.2。设置要安装的 Ubuntu 映像

*   点击“设置”,然后点击“存储”
*   单击右上角的“添加光驱”
*   选择操作系统映像(。iso)您下载了
*   单击确定

[![](img/d946c1c87dd37589ebe7cd551cb92e52.png "Storage setup")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Kh8OPtqW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sc27y1ps1m7kikugv5wb.png)

# 3。创建剩余的虚拟机

使用以下信息重复步骤 1 和 2

*   Name: kubenode1, kubenode2, kubenode3
*   内存:2048GB(或更多)
*   磁盘:32GB(或更少)

[![](img/a693979de24d19dac067dfaf46380a70.png "All nodes")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r5HD_uOW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4fddk9wysfovzfu9fkew.png)

# 4。安装 Ubuntu

### 4.1 启动虚拟机(kubemaster)

点击“开始”按钮图标。

Ubuntu 安装屏幕将显示如下。

[![](img/da29dd103f666e28331dc9a411f09095.png "Setup1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m5I9hm49--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1j12qxi66tkk1x2wjxo8.png)

选择“安装 Ubuntu 服务器

[![](img/7c0925d0186f67bb1f5e127bc691fcfd.png "Setup2")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CH_bcgrK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d0tsq1tvwkwaavliki2a.png)

### 4.2 设置主机名

按照说明进行操作，直到出现该屏幕，并填写“kubemaster”作为主机名。

[![](img/f5e7d88486f7d10dafaa5b3b3e2e33b3.png "Hostname")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6nPQhKex--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4hkuzvn3c7n10blwpopc.png)

### 4.3 设置用户名

根据需要填写“全名”、“用户名”和“密码”。请记下这些信息。

[![](img/7cc72d96cf52183c5a27059172a7d5b5.png "Username")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PlTYSDTq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b5okajzglphrir9vylra.png)

### 4.4 其他信息

*   "加密您的主目录？":没有
*   "分区磁盘":"引导-使用整个磁盘"
*   "将更改写入磁盘吗？":是的
*   “HTTP 代理信息”:留空(如果您不是公司代理)
*   "您想如何管理这个系统上的升级？":没有自动更新
*   “软件选择”:选择“OpenSSH 服务器”(如果您希望使用 SSH 命令远程连接到您的服务器，这是必需的)

[![](img/17f9bfa4cdd782f7012e2448323485a2.png "Software")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ye9qF3b3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cr9qudcufjnja4r4p52f.png)

### 4.5 安装 GRUB 引导加载程序

选择“是”，然后选择“完成安装”

[![](img/a0eff2ef31c09e18c93514cca001e08f.png "GRUB")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---AKexQ80--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gykthxw2j1pdva1up3ni.png)

# 5。为其余虚拟机安装 Ubuntu

对“kubenode1”至“kubenode3”重复步骤 4

**注:**

> *   Don't forget to step 4.2
> *   Change the hostname from "kubenode1" to "kubenode3", and all virtual machines can have the same username and password.

# 总结

现在，您拥有了所有带网络的虚拟机，并准备好在下一步的[中设置 kubernetes 集群。](https://dev.to/pongsatt/setup-kubernetes-cluster-using-kubeadm-hpb)