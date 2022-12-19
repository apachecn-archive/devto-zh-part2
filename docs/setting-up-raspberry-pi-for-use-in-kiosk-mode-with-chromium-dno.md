# 使用 Chromium 设置 Raspberry Pi 用于 kiosk 模式

> 原文：<https://dev.to/michaeljolley/setting-up-raspberry-pi-for-use-in-kiosk-mode-with-chromium-dno>

最近，我们的一个客户找到我们，要求我们开发一个可以在 Raspberry Pi 上运行的应用程序，以便在他们所有设施的信息亭中使用。我们最终用 Angular 编写了一个 web 应用程序，它们可以通过 Chromium 运行。

他们的要求之一是 Pi 不应该进入睡眠状态，并且对他们的用户永远在线。虽然我们已经成功推出了该系统，但我有一种感觉，其他人将来也会想使用同样的功能。因此，事不宜迟，下面是我们在设置每台设备以 kiosk 模式运行时使用的步骤:

### 假设

出于我们的目的，我们将假设您已经让您的应用程序在 [http://localhost:5000](http://localhost:5000) 上运行并部署得很好。

我们还将使用 Pi 的默认操作系统 Raspian。

### 先决条件

首先，让我们安装一些先决条件。

```
sudo apt-get install x11-xserver-utils unclutter 
```

Enter fullscreen mode Exit fullscreen mode

### 发射铬

我们将在启动后使用以下设置启动 Chromium:

*   隐姓埋名模式(这样它就不会在你不可避免地切断电源后试图重启你的最后一次会话)
*   信息亭模式
*   关闭挤压(这样用户就不能放大/缩小)
*   过度滚动历史导航(禁止用户在浏览器中向左/向右滚动以后退/前进)

为此，我们将在**中编辑**自动启动**文件。config/lxsession/LXDE-pi/** 。所以用
导航到它

```
cd /home/pi/.config/lxsession/LXDE-pi/ 
```

Enter fullscreen mode Exit fullscreen mode

然后用 nano 编辑成这样:

```
sudo nano autostart 
```

Enter fullscreen mode Exit fullscreen mode

一旦你在那里，添加这一行到文件的结尾:

```
@chromium-browser --kiosk --incognito --disable-pinch --overscroll-history-navigation=0 http://localhost:5000 
```

Enter fullscreen mode Exit fullscreen mode

这将使用我们上面提到的设置启动 Chromium，并立即将它定向到您的站点。

### 关闭屏保，隐藏光标

当您仍然在编辑**自动启动**文件时，添加以下内容:

```
@xset s noblank
@xset s off
@xset -dpms
@unclutter -idle 0.1 -roo 
```

Enter fullscreen mode Exit fullscreen mode

然后保存您的更改并退出**自动启动**文件。

### 保持清醒

现在，为了防止您的 Pi 睡着，我们将编辑 **lightdm.conf** 文件。

```
sudo nano /etc/lightdm/lightdm.conf 
```

Enter fullscreen mode Exit fullscreen mode

将 **xserver-command** 行修改为:

```
xserver-command= X -s 0 -dpms 
```

Enter fullscreen mode Exit fullscreen mode

然后保存并退出。

### 禁用 WiFi 电源管理

Pi 的 WiFi 我们不换也会休眠。通过用
编辑 **rc.local** 文件来修改它

```
sudo nano /etc/rc.local 
```

Enter fullscreen mode Exit fullscreen mode

在*出口 0* 前添加以下行并保存:

```
iwconfig wlan0 power off 
```

Enter fullscreen mode Exit fullscreen mode

### 你在做生意

剩下的就是重启你的 Pi 了。一旦重启，你将直接加载到 Chromium，查看你的本地应用程序。

如果您发现任何“问题”或对上述步骤的改进，请务必留下评论！