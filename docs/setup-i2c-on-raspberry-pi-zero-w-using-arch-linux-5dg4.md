# 使用 Arch Linux 在 Raspberry Pi Zero W 上设置 i2c

> 原文：<https://dev.to/ladvien/setup-i2c-on-raspberry-pi-zero-w-using-arch-linux-5dg4>

本文建立在前一篇文章的基础上，在那篇文章中，我为 Raspberry Pi Zero W 设置了 Arch Linux。

让我们不要停下来，让 I2C 继续前进，这样我们就可以与一些很酷的硬件进行交互。

## 1。安装 sudo

如果你遵循了我之前的关于在 Raspberry Pi 上安装 Arch Linux 的指南，那么你将得到一个裸机系统，这很好。没有多余的脂肪。但有时脂肪是需要的，它给我们曲线，曲线是美丽的....我觉得这个比喻正在瓦解。简而言之，我们需要额外的包来完成工作。

我们需要的第一个包是`sudo`

*   [在 Arch Linux 上安装 Sudo](https://wiki.archlinux.org/index.php/sudo#Installation)

它将允许我们轻松地管理文件权限。

首先，确保你的系统是最新的。为此，我们将以 root 用户身份登录。您可以通过键入`su`后跟根用户的密码来实现这一点，对于准系统 Arch Linux 安装来说，这个密码是`root`。

```
$ su
Password: root 
```

Enter fullscreen mode Exit fullscreen mode

哦，如果你还不明白的话，`alarm`代表 Arch Linux ARM。

接下来，我们需要更新软件包库和系统。

```
pacman -Syu 
```

Enter fullscreen mode Exit fullscreen mode

按下回车键后，它应该是这样的:

```
root@alarmpi alarm]# pacman -Syu
:: Synchronizing package databases...
 core                                                        179.0 KiB   448K/s 00:00 [#################################################] 100%
 extra                                                      1982.8 KiB  1279K/s 00:02 [#################################################] 100%
 community                                                     4.0 MiB  1689K/s 00:02 [#################################################] 100%
 alarm                                                        35.0 KiB   583K/s 00:00 [#################################################] 100%
 aur                                                           6.0 KiB  0.00B/s 00:00 [#################################################] 100%
:: Starting full system upgrade...
resolving dependencies...
looking for conflicting packages... 
```

Enter fullscreen mode Exit fullscreen mode

它应该给你一个更新和升级候选包的列表，并提示你确认更新。去吧，答应吧。

现在我们应该安装好`sudo`

```
$ pacman -S sudo 
```

Enter fullscreen mode Exit fullscreen mode

即使安装了 sudo，我们仍然需要将主用户`alarm`添加到 sudo'er 组中。这实际上给了`alarm`用户超级用户的权力。

*   [Arch Linux Sudo 配置](https://wiki.archlinux.org/index.php/sudo#Configuration)

现在，sudo 的工作方式是将用户添加到一个特殊的 Linux 组中。任何加入该群组的人都将被授予超级用户权限。要获得当前在 sudo 组中的人的列表:

```
sudo -ll 
```

Enter fullscreen mode Exit fullscreen mode

您应该得到类似于
的东西

```
User root may run the following commands on alarmpi:

Sudoers entry:
    RunAsUsers: ALL
    Commands: 
```

Enter fullscreen mode Exit fullscreen mode

好了，让我们将报警用户添加到 sudoer 组中。

类型

```
EDITOR=nano visudo 
```

Enter fullscreen mode Exit fullscreen mode

这应该允许您编辑 visudo 文件并向 sudoers 添加 alarmpi。哦，visudo 文件的写权限仅限于 root 用户，所以如果你已经从 root 用户切换回 alarmpi，你需要再次运行`su`并在编辑该文件之前以 root 用户身份重新登录。

让我们找到将用户添加到 sudo'er 组的条目。

找到看起来像这样的部分:

```
##
## User privilege specification
##
root ALL=(ALL) ALL 
```

Enter fullscreen mode Exit fullscreen mode

并在根条目的正下方添加`alarm ALL=(ALL) ALL`。编辑后应该是这样的。

```
##
## User privilege specification
##
root ALL=(ALL) ALL
alarm ALL=(ALL) ALL 
```

Enter fullscreen mode Exit fullscreen mode

然后按 CTRL+O 写入更改，按 CTRL+X 退出。

在我们检查所做的更改之前，我们需要退出我们的根会话。

```
exit 
```

Enter fullscreen mode Exit fullscreen mode

这应该能让你回到闹钟时间。为了查看您，报警用户现在被添加到 sudoer 组类型

```
sudo -ll 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，你会得到这个输出

```
User alarm may run the following commands on alarmpi:

Sudoers entry:
    RunAsUsers: ALL
    Commands:
        ALL 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们现在可以访问所有命令。Booyah！

我们可以通过键入:
来做一个硬测试

```
sudo ls 
```

Enter fullscreen mode Exit fullscreen mode

我们应该得到

```
We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for alarm: 
```

Enter fullscreen mode Exit fullscreen mode

键入闹钟用户密码(如果您没有更改密码，则密码为 alarm)。

## 2。安装所需的软件包

```
pacman -S git python2 i2c-tools base-devel python2-distribute python2-pip 
```

Enter fullscreen mode Exit fullscreen mode

使用 Python 的包索引(pip)安装 Raspberry Pi GPIO 支持

```
sudo pip2 install RPi.GPIO 
```

Enter fullscreen mode Exit fullscreen mode

## 3。安装 raspi-config

```
sudo pacman -S xorg-xrandr libnewt
git clone https://aur.archlinux.org/raspi-config.git
cd raspi-config
makepkg -i 
```

Enter fullscreen mode Exit fullscreen mode

使用 Raspi-Config 工具启用 I2C

```
sudo raspi-config 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/5f142b8c279091b812b2d614495e1c62.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j0sVtVkq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/rasp-config.png)

选择“接口选项”并启用 I2C。

注意:回顾这些说明，我注意到当我开始`raspi-config`时，我收到了这个警告:

`/usr/bin/raspi-config: line 997: warning: command substitution: ignored null byte in input`

当我试图启用 I2C 时，它给出了这个错误。

`* Failed to read active DTB`

但它似乎仍然做了工作。有时间我会做更多的调查。

现在，我们需要重启系统来注册所有东西。

## 4。测试 I2C 设置

我们*应该*都准备好了。试试跑

```
sudo i2cdetect -y 1 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，那么你应该得到

```
[alarm@alarmpi ~]$ sudo i2cdetect -y 1
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- -- 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们只需要将一个 I2C 设备连接到总线上，我们应该知道该设备的十六进制地址。