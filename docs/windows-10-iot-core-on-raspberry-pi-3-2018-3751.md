# 树莓 Pi 3 (2018)上的 Windows 10 物联网核心

> 原文：<https://dev.to/jeikabu/windows-10-iot-core-on-raspberry-pi-3-2018-3751>

决定回去重新访问我的[关于使用 OSX 在树莓 Pi 3](https://rendered-obsolete.github.io/2017/12/24/windows-10-iot-core-on-raspberry-pi-3.html) 上安装 Win10 物联网核心的帖子(不使用[物联网核心仪表板](https://developer.microsoft.com/en-us/windows/iot/Downloads))。更新使用最新版本的:OSX、物联网核心、在线资源等。

使用与上次相同的设备:

*   [树莓 Pi 3 型号 B](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/)
*   [7 英寸触摸屏](https://www.raspberrypi.org/products/raspberry-pi-touch-display/)
*   [运行 OSX High Sierra 的 15 英寸 Macbook Pro](https://support.apple.com/kb/SP756?locale=en_US)(10 . 13 . 6)
*   V2 sate chi 多端口适配器 (USB-C)

## 图像准备

微软有一些关于用 [dism](https://docs.microsoft.com/en-us/windows/iot-core/connect-your-device/dism) 代替 Dashboard 的文档。

与上次类似，我们需要获得一个物联网核心映像(an [ffu](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/deploy-windows-using-full-flash-update--ffu) )，然后将其转换为可与 [dd](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/dd.1.html) 一起使用的 img(可在 OSX、Linux 等上找到)。).

1.  定位物联网核心构建:
    *   [试试这个链接](https://go.microsoft.com/fwlink/?LinkId=846058)
    *   如果这不起作用，在“最新 Windows 10 物联网核心版本”下的[物联网核心下载页面](https://developer.microsoft.com/en-us/windows/iot/Downloads)中，应该会有一个“树莓 Pi 2 & 3”
2.  将 iso(在编写`17134.180410-1804.rs4_release_amd64fre_IOTCORE_RPi.iso` ) **下载到 Windows 机器**
3.  双击 iso 来挂载它并在里面运行`Windows_10_IoT_Core_for_RPi.msi`
4.  点击安装程序将文件安装到`C:\Program Files (x86)\Microsoft IoT\FFU\RaspberryPi2\`。
5.  下载 [ffu2img.py](https://github.com/t0x0/random) ( [文档](https://github.com/t0x0/random/wiki/ffu2img))
6.  确保您使用的是 [Python 2.7](https://www.python.org/download/releases/2.7/) 并转换图像:

```
 python ffu2img.py <path>/flash.ffu [output_filename] 
```

Enter fullscreen mode Exit fullscreen mode

图像转换需要一两分钟，并在与 ffu 相同的位置生成一个 **flash.img** 文件(如果你省略`output_filename`)。

ffu2img 库也包含 python 3 的 py3 脚本，但我没有尝试。

## 将镜像写入 SD 卡并启动

这一次[raspberrypi.org 的 Mac 指令](https://www.raspberrypi.org/documentation/installation/installing-images/mac.md)没有发出就工作了。

*   我的 SD 卡是 **/dev/disk4**
*   我确保**卸载**(不是*弹出*)所有`disk4`卷(在**磁盘工具**中选择一个卷并检查**设备**)。一个或多个可能会说`disk4sX`
*   用`sudo dd bs=1m if=PATH/flash.img of=/dev/rdisk4 conv=sync`写入 img(大约需要 5 分钟)
*   在**磁盘工具**(或者通过运行`df`)中，验证在 SD 卡上创建了几个分区(/dev/disk4s1、disk4s2 和 disk4s5)
*   弹出所有卷，将 SD 卡插回 Raspberry Pi，然后开机

## 物联网核心设置

去年我写了:

> 碰巧的是，我手头没有一个 USB 键盘来将设备接入 wifi。
> 
> 使用以太网电缆将设备直接连接到我的笔记本电脑，我从触摸屏的左下角获得了设备的本地链接地址。现在，通过将我的笔记本电脑浏览器指向`http://169.254.236.118:8080`(默认用户名/密码为`Administrator` / `p@ssw0rd`)，就可以访问该设备的管理门户了。

这次我是有备而来的，但我怀疑同样的体操也能奏效。

通过以太网连接 Pi，得到一个 DHCP 地址(`192.168.2.39`)，将一个浏览器指向`192.168.2.39:8080`(默认用户名/密码与之前相同)。其余基本相同:

在左侧面板中，选择**连接- >网络**，可以配置 wifi AP:

[![](img/423f0b1959971b0594f326cd16de2a51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EPA_vpjk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/iot_dashboard_network.png)

最初，显示屏是上下颠倒的(假设 HDMI 端口是“向上”的，就像使用[触摸屏外壳](https://www.amazon.com/Raspberry-Pi-7-Inch-Touch-Screen/dp/B01GQFUWIC)时一样)。要解决这个问题，在左侧面板中选择**设备设置**，在**显示方向**中选择`Landscape (Flipped)` :

[![](img/a89ca57e26451d2c2cf76bff9836ca1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pJS0XWWe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/iot_dashboard_orientation.png)

现在终于要开始那个长期逾期的项目了。