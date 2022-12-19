# 如何在 Windows 主机下调整 VirtualBox Linux VDI 磁盘的大小

> 原文：<https://dev.to/ech0server/how-to-resize-a-virtualbox-linux-vdi-disk-under-windows-host-2d1p>

我通常为我的虚拟机设置大量的空间，但是这次我创建了一个只有 15GB 动态分配空间的 Ubuntu 虚拟机。我想用它“只是为了测试东西”，我知道我可能会弄得一团糟，虚拟机不会持续太久。但是，一切都很顺利，我最终将它作为我现在的“prod”环境，这是客人开始抱怨剩余空间有限的地方，我在 Google 中快速搜索，得到了以下内容:

1.  关闭您的虚拟机
2.  做一个你 VDI 的拷贝，你就可以 ctrl+c，ctrl+v
3.  VirtualBox 附带了一个名为 VBoxManage.exe 的实用程序，它通常位于`"c:\Program Files\Oracle\VirtualBox\VBoxManage.exe"`
4.  要将 VDI 的大小修改为 25GB，请执行以下命令:

`C:\path\of\your\vdifile\"c:\Program Files\Oracle\VirtualBox\VBoxManage.exe" modifyhd yourvdifilename.vdi --resize 25000`

输出:

`0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%`

启动你的访客虚拟机，在我的例子中，它自动注册了新空间，但是如果你的访客没有注册，你可以按照这里的详细说明:[http://derekmolloy.ie/resize-a-virtualbox-disk/](http://derekmolloy.ie/resize-a-virtualbox-disk/)

支持 derekmolloy，我是从哪里得到这些信息的