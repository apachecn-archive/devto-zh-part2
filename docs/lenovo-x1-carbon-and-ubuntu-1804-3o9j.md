# 联想 X1 碳和 Ubuntu 18.04

> 原文：<https://dev.to/jonfriesen/lenovo-x1-carbon-and-ubuntu-1804-3o9j>

> **更新 2022 年 9 月 17 日**
> 
> 这篇文章严重过时。最新的 LTS Ubuntu 版本对联想 X1C6 有很好的支持。我将不再更新这篇文章。

开箱即用，联想 2018 款 X1 Carbon 就是一台漂亮的机器。考虑到 Windows 的设计，它留下了一些调整，以使其在大多数 Linux 发行版上都能正常工作。

> 随着操作系统/硬件支持的增加，这篇文章将会更新。

## 更好的 WQHD 支持

Linux 在高分辨率上做的不好，至少，现在还不行。我们可以期待在不久的将来实现分数缩放，但它看起来像是上游的一个相当重要的变化。

> 有希望通过[这些](https://gitlab.gnome.org/GNOME/mutter/merge_requests/3) [两个](https://gitlab.gnome.org/GNOME/gnome-shell/merge_requests/5)代码的改变来实现未来的分数缩放支持

现在，我用一些简陋的配置来凑合:

1.  我正在运行 Ubunut 18.04(在 Gnome 上)，安装 gnome tweak 工具

```
sudo apt install gnome-tweak-tool 
```

Enter fullscreen mode Exit fullscreen mode

1.  在“字体”下，将缩放比例增加到 1.6(或任何能让你的船浮动的值)
2.  打开 Ubuntu 显示设置，缩小比例到 100%
3.  在 Ubuntu dock 设置中，根据您的喜好调整大小

这应该提供一个可以忍受的体验，直到我们得到真正的分数缩放。菜单栏图标仍然很小，一些应用程序很难使用，但大多数工作很好。

## 支持睡眠&续航

**更新-2018 年 9 月 7 日:**联想发布了新的 BIOS 版本 1.30，该版本提供了修复该问题的能力。

要更新您在 Linux 上运行的 BIOS 并重启您的计算机，更新过程大约需要 5 分钟:

```
fwupdmgr refresh
fwupdmgr update 
```

Enter fullscreen mode Exit fullscreen mode

更新完成后，重启并将您的`Sleep State`设置为 linux，它位于:

```
Config > Sleep State > Linux 
```

Enter fullscreen mode Exit fullscreen mode

按 F10 保存并退出。

**警告:以下步骤不是必需的，但保留作为历史参考**

X1C6 本身不支持 S3 睡眠状态。这在[联想论坛](https://forums.lenovo.com/t5/Linux-Discussion/X1-Carbon-Gen-6-cannot-enter-deep-sleep-S3-state-aka-Suspend-to/td-p/3998182)上讨论得非常详细，看起来我们不会很快得到 BIOS 补丁。但是这并没有阻止几个黑客制作他们自己的。

此补丁更新了[dsdt](https://wiki.archlinux.org/index.php/DSDT)以支持 S3，这显著延长了您的深度睡眠电池寿命。

> **更新 2018 年 8 月 1 日——斐济——flo 创作花式剧本**
> 
> 斐济-flo 创建了一个很好的小脚本，可以在这里很快应用这个补丁: [x1carbon2018s3](https://github.com/fiji-flo/x1carbon2018s3)

翻到[斐济-flo 的](https://delta-xi.net/#056)说明，但之前盖住我的笔记。

以下是我在应用补丁时的一些注意事项:

*   确保您有最新版本的`iasl`
*   在第五步中，我在第 7 块上失败了，而不是在文章中的第 6 块，但是使用了为如果第 6 块失败留下的指令解决了它(删除生成的 dsdt.dsl 中的 2 个“1”行)
*   在第 8 步中，被添加的`/acpi_override`是你在第 8 步中复制到`/boot`的`acpi_override`的位置，所以这个添加实际上应该是`/boot/acpi_override`

可以通过禁用 BIOS 中的某些硬件来延长电池寿命:

*   存储卡插槽(这是最重要的)

```
Security > I/O Port Access > Memory Card Slot 
```

Enter fullscreen mode Exit fullscreen mode

*   指纹识别器(Linux 还不支持)

```
Security > I/O Port Access >Memory Card Slot 
```

Enter fullscreen mode Exit fullscreen mode

*   无线广域网(我不使用这个，担心它仍然会轮询 SD 卡插槽，因为它们是相同的)

```
Security > I/O Port Access > Wireless WAN 
```

Enter fullscreen mode Exit fullscreen mode