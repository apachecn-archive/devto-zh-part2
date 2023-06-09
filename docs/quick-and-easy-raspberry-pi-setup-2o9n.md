# 快速简单的树莓 Pi 设置

> 原文：<https://dev.to/osblaineora/quick-and-easy-raspberry-pi-setup-2o9n>

所以，你有一个闪亮的新的树莓 Pi，你想在上面安装 Linux。

这是一个简短的指南，帮助您安装 Raspbian 并为 Wifi 和 SSH 访问进行配置。即使您没有将键盘或显示器连接到您的 Pi，您也应该能够遵循本指南。

注意:为了完成本指南中的所有步骤，您需要能够访问和编辑 ext4 文件系统中的文件。我将用[ext4]标记这些步骤。对于 Windows 系统，这可能需要额外的步骤和/或软件。

首先，你需要下载几个文件。

*   我通常从 raspberrypi.org 下载图片。有两个图像可用。桌面映像带有一个 GUI 前端和一些预装的应用程序。lite 映像是一个“无头”安装，这意味着你将引导到一个命令提示符，并且没有安装 GUI 桌面。本指南适用于任何一个版本，请选择您想要的版本。
*   使用蚀刻机可以很容易地将图像刷新到你的 sd 卡上。

### 将 SD 卡放入电脑

在接下来的几个步骤中，请将 SD 卡放在电脑中。在我们进入下面的“启动您的 Pi”部分之前，您不会将卡放入您的 Pi。

##### 闪存 Raspbian 到 SD 卡

运行蚀刻机:

1.  选择上面下载的 Raspbian zip 文件。
2.  从列表中选择您的 SD 卡。警告:Etcher 尽最大努力确保它只显示列表中的 SD 卡，但你应该始终确保它是你要安装的卡，所选的驱动器将被格式化！
3.  点击闪光灯。

如果你打开你最喜欢的文件浏览器，你应该会看到两个新的驱动器列表。

*   靴子
*   rootfs [ext4]

##### 启用 SSH 访问

1.  换上靴子。(修改下面的“cd”命令，使其与系统的引导路径相匹配。)
2.  创建一个名为“ssh”的空文件。

```
cd boot touch ssh 
```

就是这样。第一次启动时，您的 Pi ssh 会自动启用。(先别开机。)

##### 无密码连接

过一段时间后，每次 SSH 或 SCP 到您的 Pi 时都必须键入密码的做法就过时了。如果你不介意每次都输入密码，你可以跳过这一步。

将您的公钥添加到 Pi [ext4]中:

1.  找到您想要使用的 ssh 公共密钥(例如~/。ssh/id_rsa.pub)。如果你还没有，你可以遵循本指南中的步骤。
2.  换成 rootfs 上的 pi home。(修改下面的“cd”命令，以匹配您系统上的 rootfs 路径。)
3.  创建。ssh 目录。
4.  将您的公钥附加到 authorized_keys 文件中。(您可以重复此步骤，根据需要为要使用的不同系统添加任意多的键。)

```
cd rootfs/home/pi install -d -m 700 .ssh cat ~/.ssh/id\_rsa.pub \>\> .ssh/authorized\_keys 
```

如果你不能从你的操作系统访问 ext4 分区，你可以在你启动 Pi 后在 raspberrypi.org 遵循[这个指南。](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md)

##### 启用 Wifi【ext 4】

1.  换成 rootfs/etc/wpa_supplicant。
2.  编辑 wpa_supplicant.conf。(你可能需要 sudo 来编辑这个文件。)
3.  将以下内容附加到文件的末尾。将“您的 SSID”和“您的 WPA 密码”替换为连接到 Wifi 的值。

```
network={ ssid="Your SSID" psk="Your WPA Password" key\_mgmt=WPA-PSK } 
```

1.  保存文件。

如果您不能从您的操作系统访问 ext4 分区，那么您可以在启动 Pi 后按照这些指令进行如下更改。

1.  将键盘和显示器连接到您的 Pi。
2.  更改为/etc/wpa_supplicant。
3.  继续上面的步骤 2。

现在是时候了

### 启动你的 Pi

把 SD 卡放在你的电脑里，然后启动它。

如果您没有将您的 Pi 连接到显示器，您应该能够从您的 Wifi 路由器管理页面获取其 IP 地址。

1.  用 ssh 连接。
2.  更改默认的 pi 用户密码。(默认密码:*覆盆子*)。即使我们使用公钥设置了访问权限，Pi 仍然可以使用密码进行访问，因此设置一个新密码是个好主意。
3.  运行更新。

```
ssh pi@192.168.0.34 passwd sudo apt-get update 
```

### 做些有趣的事

至此，您已经有了一台小型的 Linux 机器，可以用于您的项目了。去做些有趣的事情吧。

如果您希望我在指南中添加其他配置，请留下您的评论。