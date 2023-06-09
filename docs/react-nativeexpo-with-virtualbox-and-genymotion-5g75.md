# 用 VirtualBox 和 Genymotion 反应 Native/Expo。

> 原文：<https://dev.to/ghost/react-nativeexpo-with-virtualbox-and-genymotion-5g75>

### 用 VirtualBox 和 Genymotion 反应 Native/Expo

[![](img/4af20ea69d5ceddaf913bbf14b3182e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nsLV_s8E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3ik69-pLPMW1iSr76u37sw.png)

我的家用电脑运行 Windows 操作系统是为了各种便利，比如玩游戏。然而，对于开发，我运行 Ubuntu 虚拟机(VM)和 Genymotion(在 Windows 上)来测试我的应用程序。Genymotion 也使用 VirtualBox 运行其 Android 模拟器。所以我们解决了如何让运行在同一台主机上的两个虚拟机互相通信(Ubuntu 和 Android Emulator)。

**请注意:**这也适用于 VMWare Player。

### 解

在设置虚拟机时，有几个网络选项可供我们选择。

*   **NAT** :允许你的虚拟机与外界(出站网络)通信，但是你的主机(Windows)没有办法访问这个网络
*   **桥接**:允许您的虚拟机使用主机的以太网适配器访问网络。然而，这增加了一点复杂性，因为您的虚拟机需要在网络上拥有自己的身份，本质上它被视为另一台物理机。
*   **纯主机**:充当其他两个选项的混合体。它允许您的虚拟机与您的主机进行通信。

因此，基本上，我们让多个虚拟机相互通信的唯一方法是打开仅用于主机的网络(在所有虚拟机上)。这允许它们与主机通信，主机将充当“桥梁”(没有双关语)。

[![](img/5cefe6428c662f08545570f0ec24aa07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RCetERr1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/511/1%2AUrriO7XXBGn_a-9wwAbyvQ.png)T3】图二:网络图

### 先决条件

*   安装在主机上的 VirtualBox，[https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)
*   Ubuntu VM 中的一个 Expo app，[https://docs . Expo . io/versions/latest/workflow/create-react-native-app](https://docs.expo.io/versions/latest/workflow/create-react-native-app)
*   在主机上安装和设置 Genymotion，[https://www.genymotion.com/desktop/](https://www.genymotion.com/desktop/)

### 虚拟盒

*   打开 VirtualBox
*   点击开发虚拟机并打开“设置”。
*   转到“网络”并设置以下两个适配器，如图 1 所示
*   适配器 1:“仅主机适配器”
*   适配器 2:“NAT”

NAT 允许 Ubuntu VM 使用主机访问互联网，它允许它与外界通信。仅主机适配器允许 Ubuntu VM 与其他 VM 通信，例如 Genymotion Android 模拟器。

[![](img/cd010ba15d7e781194611d3b92a35793.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O-PATqAR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/647/1%2AW1JbTIejKC1rYigUIfZySg.png)

[![](img/6fa6aea0e8ed5e6b2c29ba44426b0048.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V7qHlGW4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/647/1%2AbFj3SryuLlej1xe0qSZOJg.png) 

<figcaption>图 1: Ubuntu VM 网络设置</figcaption>

### **【genomition】**

Genymotion 网络设置与 Ubuntu VM 非常相似，如图 2 所示。请注意，仅主机适配器可以是相同的(即#7)。

[![](img/98a01336d5b3921a9fa9e86da53736a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dPGRRqCV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/647/1%2A-kCdd8uvdThdKsUju89m5g.png)

[![](img/f7044c2379cb284a340edb156d51cab0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yvQO3j3z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/647/1%2A-F9iQFWcClEcUPpi7h_RaQ.png) 

<figcaption>图 2: Android 模拟器 VM 网络设置</figcaption>

### 世博会

*   启动您的 Genymotion Android 模拟器。
*   启动你的 Ubuntu 虚拟机，打开一个终端。
*   把 IP 地址换成你自己的，你可以在安卓模拟器上找到你的 IP 地址。如图 3 所示。

```
adb connect 192.168.112.101
exp start
exp android 
```

[![](img/a58c6bdc07cb1c75ad8c0d8936daf340.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y1TahoGL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/817/1%2AheO6Tlq7Rt3XkaXQWZp4jg.png) 

<figcaption>图 3: Genymotion Android 模拟器的 IP 地址</figcaption>

好了，现在你应该可以看到你的应用在 Genymotion Android 模拟器上运行了。您可以在下面的图 4 中看到如何启动应用程序。

[![](img/1f4c2839307e461159ed7bf63ff6e33b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dk1yi4G0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AU5PKAS0Kju68SDh9Ph7vxg.gif) 

<figcaption>图 4:在安卓模拟器上启动 Expo 应用</figcaption>

### 附录

*   用[https://www.screentogif.com/](https://www.screentogif.com/)创建的 gif
*   [关于 VirtualBox 联网的详细帖子](http://bertvv.github.io/notes-to-self/2015/09/29/virtualbox-networking-an-overview/)
*   用 [draw.io](https://www.draw.io/) 制作的图纸