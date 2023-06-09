# 在 MAC 层理解 ARP

> 原文：<https://dev.to/onmyway133/understanding-arp-at-the-mac-layer-2a14>

[![](img/ffe37d43ee7aa60e55195662cdf48e29.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---SFpXVS3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AjTHJfoEuoPsG_YFa.jpg)

有一种技术叫做 ARP 欺骗。

ARP(地址解析协议)是一种链路层协议，用于将网络层地址(IP)解析为链路层地址(MAC)。

当我们要向另一台主机(同一局域网中的计算机，或网关)发送 IP 包时，我们必须知道目的 IP 地址。为了让数据包到达目的地，必须将目的 IP 地址转换为相应的 MAC 地址，以便在数据链路层处理传输。要在 ARP 表中设置 IP-MAC 映射，我们必须首先将 ARP 请求作为广播请求发送到局域网。在愉快的情况下，在 ARP 请求中有 IP 地址的那个将回复我们他的 MAC 地址。

不幸的是，ARP 是一种无状态协议。这意味着我们继续接受 ARP 回复并覆盖旧的，即使它们还没有过期。更糟糕的是，ARP 没有定义任何认证方法来检查回复是否来自可信的那个(我们希望接收回复的那个)。这为攻击者提供了实施 ARP 欺骗的机会。

### ARP 欺骗背后的理论

从局域网中的机器上，攻击者将他的 MAC 地址和目标主机(通常是网关)的 IP 地址相关联，这样发送到该目标主机的任何流量都会到达攻击者。在这里，攻击者可以决定是否修改数据包，发送或不发送。

ARP 欺骗有 3 种类型

1.  欺骗主机

2.  欺骗网关

3.  欺骗主机和网关

**查看 ARP 表**

打开命令行(Windows)或终端(Mac OSX)，然后键入

arp -a

这将列出与特定 IP 相关的 MAC 地址。请注意，由于 ARP 欺骗，一些映射可能是错误的

### 如何防范这种情况？

为了防止欺骗我们的计算机，我们可以使用软件(搜索反网络切割，反 ARP 欺骗，…)或设置自己的静态 ARP。其实那些软件都是基于设置静态 ARP 的。这样，我们为特定主机(计算机或网关)设置了静态映射 IP-MAC，操作系统肯定会忽略该 IP 的所有 ARP 回复。

在计算机主机上执行的与网关相关的静态 ARP 映射示例

192 . 168 . 1 . 1 B4-B3-62-7C-CE-55

这里 192.168.1.1 和 B4-B3–62–7C-CE-55 是网关的 IP 和 MAC 地址

为了防止欺骗网关，我们必须在网关上设置静态 ARP。转到网关/路由器接口，在其 ARP 设置中，填写特定主机的映射 IP-MAC

查找网关手册或相关指南，了解如何执行此操作。这是一种依赖于设备的方法，但原理是一样的。

在网关上执行的与计算机主机相关的静态 ARP 映射示例

2002 年 2 月 64 日至 70 日，B2 至 9 日，E1

在这里，192.168.1.2 和 64–70–02-B2–9 B- E1 是机器主机的 IP 和 MAC 地址。机器主机可以是局域网中的任何计算机

### 如何设置静态 ARP

提议说

目的主机的 IP 地址

B4-B3–62–7C-CE-55 目的主机的 MAC

本地连接我们的网络接口名称

打开命令行(Windows)或终端(Mac OSX ),然后键入

**Windows XP**

要删除特定映射

arp -d 192.168.1.1

要设置特定映射

ARP-s 192 . 168 . 1 . 1 B4-B3–62–7c-ce-55

**Windows 7 64 位**

也许你需要打开命令行作为管理角色

要删除特定映射

netsh 接口 ipv4 删除邻居 192.168.1.1

要设置特定映射

netsh 接口 ipv4 添加邻居“本地连接”192 . 168 . 1 . 1 B4-B3–62–7C-CE-55

麦克·OSX

要删除特定映射

sudo arp -d 192.168.1.1

要设置特定映射

sudo ARP-s 192 . 168 . 1 . 1 B4-B3–62–7C-CE-55

原帖[https://medium . com/fantageek/understanding-ARP-at-the-MAC-layer-464 C4 B4 b 800381](https://medium.com/fantageek/understanding-arp-at-the-mac-layer-464c4b800381)