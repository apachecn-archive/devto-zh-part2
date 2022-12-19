# 创建二进制分析虚拟机

> 原文：<https://dev.to/famasoon/create-binary-analysis-vm-3f9g>

# 创建二元分析虚拟机

我为逆向工程创建了 VM。
这是当时的笔记。

## 安装虚拟框

运行虚拟机需要虚拟机管理程序。我选择虚拟盒子。
因为，虚拟盒子好用又免费。
要安装虚拟盒子导航到此页面。https://www.virtualbox.org/wiki/Downloads

[你为你的系统选择软件包，并下载它。
运行安装程序。
如果您使用的是 mac OS，也可以使用此命令进行安装。](https://www.virtualbox.org/wiki/Downloads)

```
brew cask install virtualbox 
```

## 安装免费的 Windows 7

微软提供免费的 windows 虚拟机。这些虚拟机的有限许可证将在 90 天后到期。
要安装虚拟机，请导航至此页面。
[https://developer . Microsoft . com/en-us/Microsoft-edge/tools/VMs/](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/)
您选择 VM:

*   Windows 7(x86)
*   虚拟盒子

下载了。zip 文件，并将其解压缩。这个文件包含了。ova 文件。

接下来，打开 Virtual Box 并选择文件>导入设备。
选择。ova 文件，然后单击继续。
设置应该没问题，你点击导入。

## 安装 FLARE-VM

FLARE-VM 是用于逆向工程的基于 Windows 的安全分发。

它安装了许多逆向工程工具。

要在 Windows VM 上安装，请使用 Internet Explorer 访问以下 URL。
[http://boxstarter.org/package/url?https://raw . githubusercontent . com/fire eye/flare-VM/master/Fla revm _ malware . PS1](http://boxstarter.org/package/url?https://raw.githubusercontent.com/fireeye/flare-vm/master/flarevm_malware.ps1)

导航到上述 URL 后，会出现一个对话框询问您。
选择运行继续安装。你在控制台窗口输入密码并按回车键。
安装过程需要时间，请喝咖啡等待。
安装后，你得到了二进制分析虚拟机。
耶，你做到了！！尽情享受吧！！