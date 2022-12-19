# 有用的树莓派技巧

> 原文：<https://dev.to/lefebvre/useful-raspberry-pi-tips-4i5j>

这里有一些小技巧可以帮助你充分利用你的树莓派。

# 更新 Raspbian 操作系统

要更新 Raspbian，您可以从终端运行以下两个命令:

```
sudo apt-get update
sudo apt-get dist-upgrade 
```

# 将文件传输到树莓派

默认情况下，Pi 上安装了一个 SFTP 服务器，但是您必须使用 Pi 配置屏幕来启用 SSH。一旦启用，您可以使用任何 SFTP 客户端连接到它(我在 Mac 上使用叉车)。您需要知道您网络上 Raspberry Pi 的 IP 地址，以及您最初安装 Raspbian 时创建的用户名和密码(默认情况下，分别为“Pi”和“Raspberry”)。

# 通过 Shell 连接到树莓 Pi

您也可以在没有任何 Pi 配置的情况下使用 ssh。在 Mac 或 Linux 上，从终端使用如下命令启动 ssh:

```
ssh pi@10.0.1.194 
```

然后输入你的密码(默认为‘树莓’)。现在，您将在终端窗口中连接到 Pi。在这里，您可以轻松运行使用 SFTP 传输的 Xojo 控制台应用程序。

*注意:Windows 还没有 ssh。你需要[安装 PuTTY](https://www.putty.org) 或者在更新版本的 Windows 10 [上启用 SSH 客户端](https://www.howtogeek.com/336775/how-to-enable-and-use-windows-10s-built-in-ssh-commands/)。*

# 通过 VNC 连接到树莓派

VNC(又名远程桌面)允许您从 Windows/Mac/Linux 主计算机上使用鼠标查看 Pi 的桌面并与之交互。这对于测试应用程序很有用，但对于更容易地访问配置屏幕或以其他方式使用 Pi 也很有帮助。

Pi 有一个内置的 VNC 服务器，但是您可能需要启用它。转到 Pi 配置(Pi 菜单->首选项->Raspberry Pi 配置)，您应该会看到一个单选按钮来启用 VNC。

[![](../Images/c372d24a8eb4fd8f5a6904cf2a0b37a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hdY14VMH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dzf8vqv24eqhg.cloudfront.net/userfiles/1539/2321/ckfinder/images/rconf.png%3Fdc%3D201612011414-102)

您可以使用任何 VNC 客户端连接到 Pi。以下是一些建议:

*   [RealVNC](https://www.realvnc.com/en/connect/download/viewer/)
*   Mac 屏幕共享
    *   Finder，Go ↠连接到服务器，vnc://10.0.1.194:5901(替换您自己的 IP 地址和端口)
*   [紧绷的 VNC](http://www.tightvnc.com/)

# 制作自己的程序

想为 Pi 制作自己的程序吗？然后看看[Xojo Dojo](https://dev.to/lefebvre/xojo-dojo---free-and-easy-way-to-program-raspberry-pi-44ga)——这对学习如何在 Pi 上制作简单的程序很有帮助。或者，如果你需要功能齐全的应用程序，可以去看看  ，它开发了可以在 Pi 上运行的本地控制台、桌面和网络应用程序。