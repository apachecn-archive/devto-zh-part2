# 网络问题疑难解答

> 原文：<https://dev.to/roznet/troubleshooting-network-issues-fn6>

我在寻找新房子时的一个要求是确保到处都有以太网电缆，这样我就可以确保我有一个稳定的网络和足够的接入点，以便在每个房间都有良好的 wifi 连接。

当我在 2016 年 8 月搬进新地方时，它符合要求，但令我和我的家人沮丧的是，网络和 wifi 稳定性非常糟糕。它总是一次又一次地拖延时间。流媒体电影或音乐每次都会暂停几分钟，阅读网络新闻也会随机挂起。

我踏上了一段漫长的旅程，试图解决这个问题，这将引导我更多地了解我曾经想象过的网络…

## 2016 年 12 月:ISP 路由器

我的第一个猜测是罪魁祸首是我的 BT 路由器。虽然我已经把我的线路和所有设备从我以前的房子里搬了出来，那里一切都很好，但我怀疑这个问题可能首先是因为老一代的路由器在保持线路方面有问题。英国电信当时提供了一种支持 IPV6 的新路由器，所以我想升级可能会解决这个问题。

它没有太大的变化，网络连接仍然很差。

## 2017 年 2 月:Wifi 干扰

在我的新房子里，我可以看到邻居的许多 wifi 网络，所以我开始怀疑问题可能是干扰，我的旧 netgear 接入点不堪重负。

我没有使用 BT 路由器作为 wifi 接入点，因为 BT 线路到达地下室，这太不方便了，我在房子里有两个旧的 Netgear 接入点通过以太网电缆连接。

所以我决定升级到新的 Netgear R7000 路由器，我将它设置为接入点，并在地下室添加了一个受管 Netgear ProSafe 交换机，以确保问题根本不是来自 BT 路由器。

虽然这有助于确保所有房间的信号强度都很强，但不知何故，网络性能仍然很差。

## 2017 年 6 月:必须是线

我开始相信问题出在 BT 线路本身。(剧透:我错了)。工作中的同事告诉我 AAISP 的情况，它是一家符合 shibboleet 标准的提供商。我打电话给他们，讨论我的问题，他们告诉我，我可以切换到他们，他们有严格的监控线路，如果有缺陷，会帮我解决。我掉包了。

我不能再推荐 AAISP 了。他们很棒。那里的支持是一流的。他们有[控制页面](https://support.aa.net.uk/CQM_Graphs)，提供关于生产线上发生的事情的历史信息。

果不其然，他们的图表显示，每次几分钟内，线条不断下降。我非常兴奋。最后，诊断显示有问题，AAISP 支持人员告诉我肯定有问题，他们会帮我联系英国电信来修复线路。但是我必须做的第一件事是确保我将路由器连接到 BT 插座的“测试端口”,以确保它不是我的内部布线的问题。

我的插座不是标准的，也没有测试端口！直到我可以通过连接到那个测试端口来显示问题，BT 才会检查我的线路。不用担心，我雇了一名技术人员将我的插座升级为新的标准插座，并将路由器连接到测试插座上。

这导致控制图上的问题全部消失。这是新低。现在线路看起来工作正常，但是我的网络性能还是和以前一样差。

AAISP 的技术人员告诉我，这一定是我的路由器或网络设备的问题。所以我决定把剩下的几个部分(几个旧的开关)升级到新的。

但这并没有解决任何问题。我开始担心这与我的内部线路或以太网电缆有关。这种前景令人担忧，因为我开始想象撕开所有的墙壁来寻找电缆问题。

## 2017 年 12 月:Linux 服务器监视器

因此，如果是内部布线问题，我必须努力缩小范围。不可能是所有的电缆，希望我能看到网络的哪个部分出了问题。

我决定进入下一阶段，在网上做了一些研究后，我决定通过使用 ping 在我的网络上实现一些探测来缩小问题的范围。首先，我试图从我的 imac 上构建一个小 python 脚本，定期 ping 几个设备，看看延迟何时会改变。这清楚地表明了一些问题，但是脚本并不可靠，而且它似乎在网络的每个部分。

然后我决定买一台小型的 linux 服务器，并在上面安装一些监控软件。首先，我希望它能帮助我进一步缩小问题的范围，其次，我想进入 linux。

我订购了一台 [NUC 电脑](https://www.intel.com/content/www/us/en/products/boards-kits/nuc.html)。这些都是很棒的小硬件，价格合理，放在我的主交换机和路由器旁边作为服务器/显示器再合适不过了。我在上面安装了 ubuntu 和其他工具，还安装了[烟熏](https://oss.oetiker.ch/smokeping/)。我将其配置为从 linux 服务器 ping 我的网络中的大多数硬件设备(交换机、接入点、计算机),以查看网络的某个部分是否有问题。

图表很糟糕，很多“黑”烟和很多丢弃的包。虽然远没有 100%的损失，网络有点工作，但没有一个设备是问题:一切都是黑暗的！

## 2018 年 1 月:最后一次开关升级

完全没有主意，我决定升级我的最后一个旧交换机到一个新的。我并不认为这有多大帮助，但我真的不知道该怎么做了，并且暗暗希望问题只是一个有缺陷的设备。

当然，这并没有帮助，性能仍然很糟糕，冒烟的图形在整个网络中仍然漆黑一片。

但是升级最后一个交换机，最终引导我找到了解决方案…

## 2018 年 2 月:顿悟！

升级了最后一台交换机后，我发现我用来远程登录我的 linux 服务器的 imac(通过以太网连接)的表现比以前差了很多。虽然 wifi 设备总是显示最差的网络性能，但硬连线电脑也是如此，但程度较轻。不过，在升级了 imac 连接的最后一个交换机后，性能明显变得更差了。与 linux 服务器的连接会不断中断或停止。

所以换个新开关让我的 imac 变得更糟了？怎么回事？

我检查了交换机的连接，意识到我忘记了将交换机连接到墙上的以太网插座上！

这怎么可能呢？我可以在没有将交换机连接到内部网的情况下访问互联网吗？？？

这是最后的线索！我有一个 Sonos 系统，其中一个 Sonos 扬声器也连接到开关上，断开 Sonos 扬声器的连接会使互联网全部掉线。

Sonos 正在通过 wifi(它自己的 wifi，不是我的主网络)创建网状网络。在网上搜索后，我发现 Sonos 正在使用[生成树协议](https://en.community.sonos.com/troubleshooting-228999/sonos-and-the-spanning-tree-protocol-16973)来防止环路。但后来我想到，它肯定无法与我的 netgear 设备一起工作，并且会产生导致网络性能的间歇性随机循环。

我在网上找到了关于关闭 Sonos 设备上的 wifi 的信息，因为我所有的设备都是有线的。

从那以后，我们在家里享受到了顶级的网络和 wifi 性能，这在一定程度上要归功于所有这些升级的网络组件:)

您可以在冒烟图中看到，自从修复后，网络有多干净，并与之前的网络状况的黑烟进行比较…

[![SmokePing](img/261c2651d9f04ee956f387176456b0f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---OOnqm5x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://roznet.github.io/assets/smokeping.png)