# 用强大的 GUI 工具调整 Ubuntu Linux 的桌面设置。

> 原文：<https://dev.to/xeroxism/tweaking-desktop-settings-in-ubuntu-linux-with-powerful-gui-tools-4067>

[![ubuntu settings fossnaija](img/0ce77e10b5ff6e31786e4d3ebc5f1826.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2018/07/ubuntu-settings.png?ssl=1)

图片:化石人. COM

有几个图形调整工具用于给 Linux 桌面一些定制外观和行为。

Tweak Tools 是 Linux 桌面的设置管理器。它为用户提供了一个快速、简单且易于使用的界面，用户可以通过该界面访问桌面环境中许多有用且鲜为人知的功能和设置。

通常，Linux 桌面调整工具提供如下配置:

–字体:用于用户界面元素和标题栏。字体提示和别名，

–可选的用户界面主题，

–窗口管理行为的变化，

–图标:文件管理桌面图标、菜单和按钮中的图标

–等等。

在这篇文章中，我将向你展示如何安装两个突出的调整工具:Unity 调整工具和 gnome 调整工具。

## 安装 Unity Tweak 工具(UnTT)。

您可以使用 ubuntu 软件中心安装它，只需在搜索栏中键入(" **unity tweak tool** ")，然后点击安装:

[![unity tweak tool](img/05f347fbadd39219e6db7282a1f2c9ba.png)T2】](https://i0.wp.com/fossnaija.com/wp-content/uploads/2018/07/unity-tweak-tool.png?ssl=1)

图片:化石人. COM

或者，您可以通过键入如下安装命令来使用终端:

更新包索引:

```
sudo apt-get update 
```

安装 unity-tweak-tool deb 包:

```
sudo apt-get install unity-tweak-tool 
```

## 安装 Gnome Tweak 工具(GnTT)。

就像 UnTT 一样，GnTT 执行类似的功能，比如调整几个不能在 gnome 控制中心配置的高级 GNOME 选项。要为命令行安装，请执行以下操作:

更新包索引:

```
sudo apt-get update 
```

安装 gnome-tweak-tool deb 包:

```
sudo apt-get install gnome-tweak-tool 
```

[![gnome tweak tool](img/1375768c51f9f3398d756bdba38972ae.png)T2】](https://i0.wp.com/fossnaija.com/wp-content/uploads/2018/07/gnome-tweak-tool.png?ssl=1)

图片:化石人. COM

您也可以下载可安装的(。deb)包，取决于你的系统架构，从[到](https://pkgs.org/download/gnome-tweak-tool)。

并使用 Ubuntu 软件中心进行安装；

[![gnome tweak tool installation](img/f4e142f57406e23c2fe29ba432f3d5e5.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2018/07/gnome-tweak-tool-installation.png?ssl=1)

图片:化石人. COM

或者命令行使用:

```
sudo dpkg ~/Downloads/gnome-tweak-file-name.deb; 
sudo apt -f install 
```

快乐的 Linux！

用强大的 GUI 工具调整 Ubuntu Linux 的桌面设置。最早出现在[福斯奈亚](https://fossnaija.com)上。