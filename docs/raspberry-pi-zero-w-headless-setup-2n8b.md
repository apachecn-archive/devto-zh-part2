# RPi 零无头设置

> 原文：<https://dev.to/mister_frostee/raspberry-pi-zero-w-headless-setup-2n8b>

#### [T1】简介](#intro)

这是我在 dev.to 上的第一篇文章，希望对你有所帮助。我在 2017 年 12 月购买了一台 Raspberry Pi Zero W，除了插上电源适配器检查绿灯是否亮起之外，我再也没有碰过它。

[![alt text](../Images/f1a885da589834d9c54c3418dac950e7.png "Raspberry Pi Zero W ports")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pd_RCI1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://raspi.tv/wp-content/uploads/2016/05/Pi-Zero-1.3-top_1500.jpg)

当我第一次收到它的时候，我很惊讶，它有两个独立的 micro USB 端口，分别用于电源和 USB 输入。我还对它带有一个迷你 HDMI 端口感到失望，因为我没有迷你到普通的 HDMI 适配器或电缆来连接显示器。我知道无头设置是可能的，但我从来没有抽时间去做。我一直想用 [Node-RED](https://nodered.org/) 来做我的一些 DIY 电子项目已经有一段时间了，我想我已经推迟设置 RPi 足够长的时间了！

#### “无头”设置到底是什么？

当你想在不使用显示器或键盘的情况下设置像 Raspberry Pi 这样的设备时，你可以进行“无头”设置。与您通常使用的方式(即，使用显示器和输入设备)相比，这种设置方式需要一些额外的步骤。

#### 第一步:下载操作系统

我首先从 [Raspbian 下载页面](https://www.raspberrypi.org/downloads/raspbian/)下载了 Raspbian Stretch Lite 操作系统映像。已经决定我要做一个无头安装，我想我会得到“精简”版本，它不附带任何桌面环境。如果有必要，我可以在以后安装一个。图像的大小为 365.8 MB。

#### 第二步:将操作系统映像写入 SD 卡

我喜欢用[蚀刻机](https://etcher.io/)将操作系统镜像写入 SD 卡/USB 驱动器。下面的 GIF 向您展示了它是多么的快速和简单:

[![alt text](../Images/18770222a9c9901bcd7e9bfa58b1915a.png "Using Etcher")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fvuz3zZQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://etcher.io/static/screenshot.gif)

我用的是一个旧的 4 级 SanDisk 16 GB 微型 SD 卡，我把它和官方的 Raspberry Pi 微型 SD 适配器放在一起。将图像写入 SD 卡大约需要 10 分钟。

[![alt text](../Images/7f40e988ceb1fd93beeea3385180bb0e.png "Micro SD card & adapter")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4Pc5lUqk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ha4Nsn1.png)

#### 第三步:设置 WiFi

在没有连接显示器或任何 I/O 设备的情况下，如何将 RPi 连接到无线网络？很简单。在 SD 卡仍处于插入和安装状态的情况下，创建一个包含您要连接的网络的 SSID 名称和密钥的文件。

启动终端，导航到 SD 卡。

```
cd /media/userName/boot 
```

Enter fullscreen mode Exit fullscreen mode

创建一个名为`wpa_supplicant.conf`的文件。

```
touch wpa_supplicant.conf 
```

Enter fullscreen mode Exit fullscreen mode

在您最喜欢的文本编辑器中打开文件。不要妄加评论，我使用 nano 是因为它足以完成如此简单的事情。

```
nano wpa_supplicant.conf 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容添加到文件中:

```
country=IN
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
    ssid="mySSID"
    psk="my5up3rStr0ngP@sSw0Rd!"
} 
```

Enter fullscreen mode Exit fullscreen mode

这一切意味着什么？

`country=IN`

这是不言自明的。我在(印度)指定我使用该设备的国家及其 [ISO/IEC alpha2 代码](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Decoding_table)。

`update_config=1`

用于允许 wpa_supplicant 覆盖当前配置的`update_config`选项，如果配置发生更改(例如，使用 wpa_cli 或 wpa_gui 添加新的网络块，或者更改密码)。如果此选项设置为`0`，则 wpa_cli/wpa_gui 将无法保存新设置。

`ctrl_interface=/var/run/wpa_supplicant`

该参数允许`wpa_supplicant`拥有一个控制接口，外部程序(如`wpa_cli`或`wpa_gui`)将使用该接口读取/修改配置值。在 Linux 和 BSD 上，UNIX 域套接字有一个目录来监听来自命令行或 GUI 程序的状态信息或配置请求。默认情况下，这个目录是`/var/run/wpa_supplicant`，`wpa_cli`在尝试连接`wpa_supplicant`时会使用这个路径。

`network={`
`ssid="mySSID"`
`psk="my5up3rStr0ngP@sSw0Rd!"`

这部分被称为“网络块”。我已经简单地指定了我希望 RPi 连接到的无线网络的 SSID 和密码，因为我的网络设置非常简单(一个移动热点)。但是，如果您的网络设置更复杂，您将需要修改网络块以方便连接。根据需要，您可以设置几个网络块选项。如果您对有趣的细节感兴趣，请查看这个[示例 wpa_supplicant.conf 文件](https://w1.fi/cgit/hostap/plain/wpa_supplicant/wpa_supplicant.conf)。

如果您希望 RPi 能够连接到多个网络，这取决于它的位置，该怎么办？你可以有多个网络块！

```
# Network 1
network={
    ssid="office"
    psk="officePassword"
}

# Network 2
network={
    ssid="home"
    psk="homePassword"
}

.
.
.

# Network N
network={
    ssid="workshop"
    psk="workshopPassword"
} 
```

Enter fullscreen mode Exit fullscreen mode

如果树莓派连接到我的网络，这还不够。除非明确指定，否则 SSH 将保持禁用状态。要启用 SSH，只需在`boot`文件夹(`/media/userName/boot`)中创建一个名为`ssh`(无扩展名)的空文件。当 Pi 启动时，它会寻找`ssh`文件。如果找到，将启用 SSH，并自动删除该文件。

一切就绪！我弹出了 SD 卡，把它放进了我的树莓派。

**注意事项**:

1.  这些都是我在 Linux 下做的。如果你用的是 Windows，只需在 Windows 资源管理器中打开 SD 卡，然后跟着做。不运行`touch`和`nano`命令，而是在文本编辑器中创建一个新文件。我强烈建议你使用一个程序，在保存的时候把你的文件行尾从 DOS 转换到 UNIX。一个很棒的 Windows 文本编辑器可以帮你做到这一点，那就是[记事本++](https://notepad-plus-plus.org/download/v7.5.6.html) 。或者，如果你有 Cygwin，你可以在保存文件后简单地运行`dos2unix`。注意文件扩展名——你不希望你的文件以“.”结尾。txt "扩展！

2.  RPi 第一次启动后,`wpa_supplicant.conf`文件将被移动到`/etc/wpa_supplicant/`。如果您想要添加/修改您的网络设置，您需要对`wpa_supplicant.conf`进行这些更改，因为在此位置您拥有超级用户权限。

#### 第四步:查找 IP 地址

我打开 RPi，打开移动热点，等待连接。几秒钟之内，树莓派就连上了。好极了。但是我该如何对一个我不知道 IP 地址的设备进行 SSH 呢？有不同的方法可以解决这个问题:

##### 1:在 Linux 上，使用`arp`

ARP 代表地址解析协议。`arp`命令显示 Linux 内核的 IPv4 网络邻居缓存。使用此命令，您可以添加、删除或修改表格。

所以`arp -a`给了我这个输出:

```
gateway (142.30.10.1) at ac:d3:9f:1c:ff:23 [ether] on wlp6s0
device1 (11.210.123.60) at 9b:4c:5e:65:4d:6e [ether] on enx9ce
? (142.30.10.4) at 23:c2:dc:ae:85:9f [ether] on wlp6s0
device2 (11.210.123.62) at f8:7d:23:8a:80:4e [ether] on enx9ce
device3 (11.210.123.65) at 0f:0f:0d:0e:ca:db [ether] on enx9ce 
```

Enter fullscreen mode Exit fullscreen mode

注意，界面显示为`wlp6s0`和`enx9ce`，而不是标准的`eth0`和`wlan0`。从 v197 开始，system/udev 开始为所有本地以太网、WLAN 和 WWAN 接口分配可预测的、稳定的网络接口名称。这里是你需要知道的关于[可预测网络接口名称](https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/)的所有信息。

所以凡是以“wl”开头的都是无线局域网，凡是以“en”开头的都是以太网。上面列表中的“网关”是我用来创建热点的手机。所以那不是我想要的 IP。界面以“wl”开头的另一个项目是`?`。我不在乎它的名字是孤独的`?`，所以我试着去 142.30.10.4。

`arp -a`命令在 Windows 上也有效(惊喜！)，但与 Linux 不同的是，输出显示了您的计算机知道的或过去与之对话的所有其他系统。请注意，列表可能不完整-有些设备只有在您的计算机对其执行 pinged 操作并收到响应后才会添加到列表中。

##### 2:从你的路由器

寻找 Raspberry Pi 的 IP 地址的另一种方法是打开路由器的管理页面，查看连接设备的列表。即使您看不到设备的实际主机名，您也应该能够缩小您需要尝试登录的 IP 的范围。我在 iPhone 上设置了热点，不幸的是，它不能让你看到分配给连接设备的 IP 地址。如果你用的是安卓手机，应该可以查看每个连接设备的 IP 地址。

#### 第五步:建立 SSH 连接

我按如下方式连接到 RPi:

```
ssh pi@142.30.10.4 
```

Enter fullscreen mode Exit fullscreen mode

我收到了这条信息:

```
The authenticity of host '142.30.10.4 (142.30.10.4)' can't be established.
RSA key fingerprint is 80:6c:7e:a3:cd:2b:60:9d:55:b8:4d:3e:d3:f8:e1:32.
Are you sure you want to continue connecting (yes/no)? 
```

Enter fullscreen mode Exit fullscreen mode

成功！我用`yes`回复。

```
Warning: Permanently added '142.30.10.4' (RSA) to the list of known hosts.
pi@142.30.10.4's password: 
```

Enter fullscreen mode Exit fullscreen mode

全新安装 Raspbian 的默认用户名和密码分别是`pi`和`raspberry`。

#### 第六步:初始设置

登录后您要做的第一件事是更改默认密码。你可以用`passwd`命令来完成。系统会要求您输入当前密码，之后您必须输入新密码。

```
pi@raspberrypi:~ $ passwd
Changing password for pi.
(current) UNIX password:
Enter new UNIX password:
Retype new UNIX password: 
```

Enter fullscreen mode Exit fullscreen mode

基本操作系统映像的大小使其在 SD 卡上占用尽可能少的空间。完整安装 Raspbian 大约需要 4 GB。如果文件系统没有被显式扩展，那么在更大的 SD 卡中所有额外的可用空间都会被浪费掉。因此，我随后运行命令:`sudo raspi-config`，这将显示:

！[alt text]([http://42 bots . com/WP-content/uploads/2015/03/raspi-config-01 . jpg](http://42bots.com/wp-content/uploads/2015/03/raspi-config-01.jpg)raspi-config 屏幕)

在这里，我选择了第一个选项- `Expand Filesystem`。要理解这到底是做什么的，请阅读树莓 Pi StackExchange 上这个问题的第二个答案。

接下来，我将地区和时区设置如下:

```
sudo dpkg-reconfigure locales 
```

Enter fullscreen mode Exit fullscreen mode

树莓派基金会是一家总部位于英国的慈善基金会。因此，默认的语言环境是英语-英国。默认时区和键盘布局也适用于英国。在显示的语言环境列表中，取消选择`en_GB.UTF-8 UTF-8`，改为选择`en_US.UTF-8 UTF-8`，并点击“确定”。确认您的选择，并等待区域设置生成完成。

[![alt text](../Images/482ef1b7c3638eabb3c38376385d9cbf.png "Setting the locale")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t1aM2Ptr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://unix.cafe/wp/wp-content/uploads/2016/09/lc_locales.png)

[![alt text](../Images/26a2a1b62783ae85c829a766f3a7b700.png "Confirming the locale")](https://res.cloudinary.com/practicaldev/image/fetch/s--SGIB6_1H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Jp795pk.png)T3】

```
Generating locales (this might take a while)...
  en_US.UTF-8... done
Generation complete. 
```

Enter fullscreen mode Exit fullscreen mode

然后我重新配置了键盘布局如下:

```
sudo dpkg-reconfigure keyboard-configuration 
```

Enter fullscreen mode Exit fullscreen mode

我刚刚设置的语言环境将用于此，并且没有输出。

最后，我设置时区如下:

```
sudo dpkg-reconfigure tzdata 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我选择了亚洲，然后是加尔各答。

[![alt text](../Images/68b331560710ed6b59e11cc28442c106.png "Geographic location")](https://res.cloudinary.com/practicaldev/image/fetch/s--d64XMHSE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://abdussamad.com/files/2013/02/tzdata-reconfigure.jpeg)T3】

```
Current default time zone: 'Asia/Kolkata'
Local time is now:      Thu Mar 22 16:32:44 IST 2018.
Universal Time is now:  Thu Mar 22 11:02:44 UTC 2018. 
```

Enter fullscreen mode Exit fullscreen mode

也就这样了！

#### 下一步

我发现每次我想找到 Pi 的 IP 地址时都需要运行`arp`命令，这非常令人恼火。或者，我必须打开路由器管理页面来查找。或者，我必须用静态 IP 地址来配置它。

因此，我决定编写一个运行在 RPi 上的脚本，一旦它连接上，就会给我发送一封指定 SSID 和 IP 地址的电子邮件。我将在下一篇文章中讲述我是如何做到这一点的。

我期待你对我的帖子的评论/反馈。