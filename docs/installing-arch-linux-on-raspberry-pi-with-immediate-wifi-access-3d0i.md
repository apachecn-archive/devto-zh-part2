# 通过即时 WiFi 访问在 Raspberry Pi 上安装 Arch Linux

> 原文：<https://dev.to/ladvien/installing-arch-linux-on-raspberry-pi-with-immediate-wifi-access-3d0i>

免责声明:用 Arch Linux 为 Raspberry Pi Zero W (rp0w)设置 SD 卡的最简单的方法是使用 Linux——下面的指南将假设您可以在某个地方访问 Linux。对于 Mac 和 Windows 用户，可以在虚拟机内部使用 Linux 安装 SD 卡。网络上将会有更多关于这个话题的报道。

为 rp0w 设置 Arch Linux 最困难的部分是让 WiFi 在启动时工作。这允许通过 ssh 立即访问操作系统。这就是所谓的“无头设置”我已经用卡利语创建了做类似事情的指令。然而，当我点击 Arch 时我很幸运——因为有一个很好的家伙已经写了一个脚本来设置无头构建所需的 WPA 请求程序。

*   [Stasiana 设置 wpa_supplicant 的指令脚本](https://archlinuxarm.org/forum/viewtopic.php?f=31&t=11529)

### 1。按照 Arch Linux 说明创建 SD 卡

实际上，Arch Linux 社区唯一没有提供的信息是 rp0w 需要哪种 ARM 架构。是 armv6。

*   [树莓派 1 /零/零 W](https://archlinuxarm.org/platforms/armv6/raspberry-pi)
*   [树莓派 2](https://archlinuxarm.org/platforms/armv7/broadcom/raspberry-pi-2)
*   [树莓派 3](https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-3)

使用安装说明的一些注意事项。

*   我必须以 root (sudo)身份运行大多数命令
*   我们将在 SD 卡安装完成后，启动 rp0w 之前插入一个步骤
*   **最重要的提示**:如果你不小心选择了一个不同的设备而不是你的 SD 卡，就会发生糟糕的事情。真的。要知道你的卡是哪个设备，充分利用`fdisk -l`，它将提供所有设备的列表。你的 SD 卡和卡片上写的差不多大。例如，这是我在装有 SD 卡的 PC 上运行`fdisk -l`时得到的输出。

```
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt

Device         Start       End   Sectors   Size Type
/dev/sda1         40    409639    409600   200M EFI S
/dev/sda2     409640 578929663 578520024 275.9G unkno
/dev/sda3  578929664 586480023   7550360   3.6G Micro
/dev/sda4  586480024 586742167    262144   128M Apple
/dev/sda5  586743808 976842751 390098944   186G Linux
/dev/sda6  976842880 977105023    262144   128M Apple

Mounting
Unmounting
Cleaning up

Disk /dev/sdb: 7.5 GiB, 8053063680 bytes, 15728640 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd0ca12f8

Device     Boot  Start      End  Sectors  Size Id Type
/dev/sdb1         2048   206847   204800  100M  c W95
/dev/sdb2       206848 15728639 15521792  7.4G 83 Linu 
```

Enter fullscreen mode Exit fullscreen mode

所以，我的 SD 卡的主设备路径是`/dev/sdb`。第一个分区是`/dev/sdb1`

### 2。创建脚本以在启动时启用 WiFi

我们需要在您用来设置 SD 卡的 Linux 操作系统上创建一个脚本。这个脚本将访问 rp0w 的 Arch Linux 文件，并注入我们的 WiFi 信息。这将允许 rp0w 在启动时自动连接到您的 WiFi 路由器，因此，您可以通过 SSH 立即访问它。

在命令提示符下(在您的 PC 上，而不是 rp0w 上)

```
nano al-wpa-setup.sh 
```

Enter fullscreen mode Exit fullscreen mode

这将打开一个空白的纳米编辑器。在里面，粘贴以下内容，然后保存文件。

```
#!/bin/sh

set -e

if [[ $# -ne 3 ]] ; then
   echo "Usage: $0 </dev/disk> <ssid> <passphase>"
   exit 1
fi

DISK="$1"
SSID="$2"
PASS="$3"

if [[ ! -b "${DISK}" ]] ; then
   echo "Not a block device: ${DISK}"
   exit 1
fi

if [[ "${USER}" != "root" ]] ; then
   echo "Must run as root."
   exit 1
fi

echo Mounting
mkdir root
mount "${DISK}2" root

cat << EOF >> root/etc/systemd/network/wlan0.network
[Match]
Name=wlan0

[Network]
DHCP=yes
EOF

wpa_passphrase "${SSID}" "${PASS}" > root/etc/wpa_supplicant/wpa_supplicant-wlan0.conf

ln -s \
   /usr/lib/systemd/system/wpa_supplicant@.service \
   root/etc/systemd/system/multi-user.target.wants/wpa_supplicant@wlan0.service

echo Unmounting
umount root

echo Cleaning up
rmdir root 
```

Enter fullscreen mode Exit fullscreen mode

对于那些好奇或警惕的人来说，这个脚本有三个参数

1.  SD 卡在电脑设备树中的位置
2.  您的 WiFi 路由器的 SSID
3.  WiFi 路由器的密码

然后，它安装 SD 卡，访问设置 WiFi 所需的文件，并适当地插入连接信息。

再次感谢，斯塔西亚娜。

我们继续吧。

在我们运行脚本之前，必须给它可执行的权限。

```
chmod +x al-wpa-setup.sh 
```

Enter fullscreen mode Exit fullscreen mode

注意:如果你在构建 SD 卡的同一路径下执行脚本，那么脚本会报错

```
mkdir: cannot create directory ‘root’: File exists 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 Arch Linux 指令没有提到删除 SD 卡路径。

删除安装运行所需的路径`root`和`boot`(首先确保您不在`/`路径中)。

```
sudo rm -R boot root 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们执行它，通过`/dev/sdX`、`your_wifi_name`和`your_wifi_password`。像这样。

```
./al-wpa-setup.sh /dev/sdb wifi_name wifi_password 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，你应该会看到。

```
Mounting
Unmounting
Cleaning up 
```

Enter fullscreen mode Exit fullscreen mode

还有什么，给我留言，我会帮你排忧解难的。

### 3。连接

好吧！就是这样。现在，将 SD 卡放入 rp0w 并启动它。绿灯应该开始闪烁。

最后一个棘手的部分是在启动时知道 rp0w 分配了什么 IP 地址。等待几分钟让它连接到 wifi 后，访问路由器的管理页面。一般是 [192.168.1.1](//192.168.1.1) 。

[![](../Images/ec47a4b301650a22412093eb13bebcd3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lNzdA62S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/router_admin.png)

您需要路由器登录信息。但是一旦进入，就应该有一个类似“附加设备”的部分。在那里，您应该会看到一个“alarm”条目(代表 Arch Linux ARM)。这是您的 rp0w。

[![](../Images/0350f27aab677d29c1639a1c26c855fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bIEnQfmz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.com/images/arch_pi_address.png)

现在，在命令行输入:

```
ssh alarm@192.168.1.xxx 
```

Enter fullscreen mode Exit fullscreen mode

将`x`替换为您的树莓 Pi 的地址。如果你不知道 Raspberry Pi 的地址，你可以登录路由器寻找`ALARMPI`。

其中 xxx 是分配给 Pi 的地址。应该会提示您一个 EDSCA 警告(说是)。然后，您需要输入密码`alarm`。

```
Welcome to Arch Linux ARM

     Website: http://archlinuxarm.org
       Forum: http://archlinuxarm.org/forum
         IRC: #archlinux-arm on irc.Freenode.net
Last login: Thu Apr 12 12:18:05 2018 from 192.168.1.5
[alarm@alarmpi ~]$ 
```

Enter fullscreen mode Exit fullscreen mode

快乐拱。