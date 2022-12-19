# Mac 上的 Windows 键盘在 macOS 上使用 PC 键盘有什么比较好的方法？

> 原文：<https://dev.to/equiman/whats-the-better-way-to-use-pc-keyboard-on-macos-3k3>

我一生都在 PC/Linux 上工作，现在由于 iPhone 编译对 Xcode 的依赖，我在 macOS 上工作。

开始时，很难改变你的想法，因为控制键`win/alt/ctrl`的顺序不同`ctrl/option/command`。

我觉得还是用一些他默认配置的系统比较好。我将向你展示如何将你的电脑键盘改装成 mac 键盘。

# 简单的日语

这是 mac 键盘分布图(左边有`alt`):

[![Alt Mac Keyboard distribution](../Images/ac5c77514a6252abe4d15e2fee592d75.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AsiE-_nJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q691zbez6uugkxmuaia2.jpeg)

> **注意:**在取下任何键盘按键之前，请从电脑上拔下键盘或关闭电脑。强烈推荐看一些 youtube 上关于拆键或者清洁键盘的视频。

电脑键盘的右边有一个`alt`键。只需取下`win`和`alt`键…

[![Alt PC Keyboard distribution](../Images/c25f9f213d409aa6ac07f773c808d7ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v_vDCwsA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/52z7ya9xglwdrp7tetpf.jpeg)

…然后交换它们。Tada！！！…现在，您拥有了与 mac 键盘相同的物理(和视觉)顺序。

[![Alt PC Keyboard swap](../Images/5f16edd0358e270c5eb08cc43d2d6f74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lJz64b4i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m8xeazknd9bj9dafjrkq.jpeg)

# 重映射键

最后一步是告诉 macOS 改变这个键的行为。

转到`System Preferences → Keyboard`，选择并按下`Modifier Keys…`按钮。在列表中选择您的 PC 键盘(我的无线罗技键盘被标识为“USB 接收器”)，将`⌥ Option`切换到`⌘ Command`，将`⌘ Command`切换到`⌥ Option`，按下`OK`按钮…

[![Alt Mac Keyboard configuration](../Images/0b383531e08224a615f5395face2ca0b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2B5hlwpf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xejfrtwa4ulpvaj0zoty.png)

# 另类

更好的方法是使用 Karabiner:一个优秀的键盘定制工具。