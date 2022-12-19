# Fcitx :调整输入辅助弹出窗口的显示位置

> 原文：<https://dev.to/nabbisen/fcitx--a5g>

# Summary

[Fcitx](https://fcitx-im.org/wiki/Fcitx) 是在 Linux/Unix 系统中广泛使用的输入方法。
(其他代表性的输入方法有[iBus](https://github.com/ibus/ibus/wiki) 。 )
支持使用[Mozc](https://github.com/google/mozc) 和 Anthy 作为后端的日语输入。

Fcitx 具有在输入时显示辅助弹出窗口的功能。 作为输入辅助显示输入候补。
介绍如何在输入位置(而不是远离输入位置)下方显示这个 Fcitx 的自动图文集弹出窗口。

# problem

[针对 Manjaro Linux](https://manjaro.org/) 的[Cinnamon 桌面](https://manjaro.org/community-editions/)，构建了使用 Fcitx-Mozc 的日语输入环境。
那时，发生了根据 APP 应用程序的不同，自动图文集弹出窗口(候补窗口)会在分开的位置( APP 应用程序窗口的下方)显示的问题。

### Before: Google Chrome 中的形象

输入位置在上面，但弹出窗口(输入候选)显示在窗口外的左下方。

[![](../Images/3aceb10397dc85f524258ebc792384f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IViumO2I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8a5p8gwl6qrotlf4nwkv.jpg)

如果输入方法无法获取输入光标的位置，则可能会出现这种情况。

# Solution

安装与 GUI 相关的扩展模块。

*   fcitx-gtk2
*   fcitx-gtk3
*   fcitx-qt4
*   fcitx-qt5

**补充:只要修改作为例子显示的 Google Chrome 上的显示，GTK 就足够了。*

### Manjaro 包管理器

安装软件包。

[![](../Images/8ef2a6a93ee52f814600762acc42ce11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4J-mbPp2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/55fokxoc5f4v4mxlob08.png)

安装后重新启动 Fcitx。

[![](../Images/7655cd752df59261e90bd43317b4c08c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DHFEDHWL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rydploa2hd76fwixho0o.jpg)

**补充:如果仍然无法修复，请重新启动操作系统。*

### After: Google Chrome 中的图像

[![](../Images/4c9c8e410dfbe20c87ec443819986252.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f7gR22rp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w3bmud6cggwadnh9ky5a.png)

弹出窗口现在显示在输入位置下方