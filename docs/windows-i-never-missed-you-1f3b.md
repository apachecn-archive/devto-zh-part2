# 温都思，我从未想念过你。

> 原文：<https://dev.to/bdmorin/windows-i-never-missed-you-1f3b>

感谢 [Josh Beam](https://www.joshbeam.com/2017/11/23/making-a-bootable-windows-10-usb-drive-on-macos-high-sierra/) 提供的制作 Windows10 可引导 usb 驱动器的简单方法。

我已经有将近十年没有真正使用过 Windows 了(除了一台 steam 游戏电脑，还是 windows 7)。最近有人请我去修理一台被恶意软件彻底摧毁的惠普流电脑。我不得不把所有东西都擦掉，重新开始。我下载了一个新的 windows10 映像，发现无法将其写入 usb 驱动器。我再也没有 DVD 了，因为...为什么？！..苹果只说“使用 bootcamp 助手来创建图像”..在高 Sierra Bootcamp 助理只会写入本地硬盘。我也不能让我的 Kali vm 镜像 ISO 文件。

事实证明这非常简单，把 u 盘格式化成 windows 格式，然后用/bin/cp 把文件复制到 u 盘上。 *BOOM* usb 可引导驱动器。

谢谢你乔希。

<sub>*Unsplash*</sub>上由[伊内斯·Á·阿尔瓦雷斯·费兹](https://unsplash.com/photos/Bb_X4JgSqIM)拍摄的封面照片