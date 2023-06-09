# 在 Linux 环境下运行 Windows 应用程序。

> 原文：<https://dev.to/emma_odia/run-windows-applications-in-linux-environment-301m>

问题:如果你是一个最近从 Windows 环境移植过来的 Linux 用户，不一定是因为你鄙视 explorer(哈哈…我忍不住在这里扔阴影！).好吧，如果你足够诚实，你会承认在 Linux 环境中你错过了一些应用。这篇文章不打算讨论虚拟环境或双引导机器的使用(伪君子！哈哈…又一个阴影！)更确切地说，它是通向金矿补丁的道路，该补丁在基于 Linux 的环境中很好地集成了 Windows 包，它拥有一个甚至更甜美的名字。

听着，听着，所有寻找 windows 鸡尾酒意外收获的 Linux 啤酒爱好者，请容忍我，交换啤酒杯的味道，拿酒杯来倒一些，加入祝酒。有一个很好的包，实际上叫做 wine，我们可以用它在 Linux 环境下运行基于 windows 的包！

## 什么是酒？

根据[网站](https://winehq.org/)上的用户手册

“不同的软件程序是为不同的操作系统设计的，大多数程序不能在不是为它们设计的系统上运行。例如，Windows 程序不能在 Linux 上运行，因为它们包含系统无法理解的指令，直到它们被 Windows 环境翻译。同样，Linux 程序也不能在 Windows 操作系统下运行，因为 Windows 不能解释它们所有的指令。

Wine 使得 Windows 程序可以与任何类似 Unix 的操作系统一起运行，尤其是 Linux。在本质上，Wine 是 Windows 应用程序编程接口(API)库的一个实现，充当 Windows 程序和 Linux 之间的桥梁。把 Wine 想象成一个兼容层，当一个 Windows 程序试图执行一个 Linux 通常不理解的功能时，Wine 会把那个程序的指令翻译成系统支持的指令。"

你可以在这里阅读更多关于葡萄酒的内容

## 在你的 Linux PC 上安装 wine。

进入从可用选项列表中选择你的 Linux 发行版，你将进入一个页面，上面有安装该发行版的进一步说明。

这是安装 Ubuntu 的一步一步的方法。为此，您需要一个可用的互联网连接，总共需要 10 分钟:

首先，如果您的 Linux 环境是 64 位的，并且还不能运行 32 位架构，那么运行这个命令:

```
sudo dpkg –add-architecture i386 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将从葡萄酒存储库中安装葡萄酒包。这样我们可以从终端安装未来的版本。依次运行以下每个命令。

```
wget -nc https://dl.winehq.org/wine-builds/Release.key

sudo apt-key add Release.key

sudo apt-add-repository https://dl.winehq.org/wine-builds/ubuntu/ 
```

Enter fullscreen mode Exit fullscreen mode

如果您有 Linux Mint 版本 18.x
,请运行以下命令来代替最后一个命令

```
sudo apt-add-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ xenial main' 
```

Enter fullscreen mode Exit fullscreen mode

和在 Linux 上运行新安装一样，更新所有的包:

```
sudo apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

接下来是安装葡萄酒包本身。我建议安装稳定的软件包。运行以下命令来完成此操作:

```
sudo apt-get install --install-recommends winehq-stable 
```

Enter fullscreen mode Exit fullscreen mode

如果 apt-get 抱怨缺少依赖项，安装它们，然后重复最后两步(更新和安装)。

## 设置葡萄酒配置环境

在使用 Wine 之前，有必要创建一个虚拟的 C:驱动器来安装您的 Windows 应用程序。为此，请打开终端并输入以下命令。

```
winecfg 
```

Enter fullscreen mode Exit fullscreen mode

按照所有提示安装软件包并设置 Windows 环境，系统将提示您选择的版本。完成这一步后，wine 就已经安装在您的 Linux 环境中了，您可以通过运行:
来检查 wine 是否已经安装

```
wine --version 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 windows 应用程序

要使用 Wine 安装 Windows 应用程序，请遵循以下说明:
1。从任何来源下载 Windows 应用程序，下载。exe(可执行文件)。
2。将其放在一个方便的目录中(如桌面或个人文件夹)。
3。打开终端，将 cd 放入。exe 文件的位置。
4。输入 wine the-name-of-application . extension

```
wine realplayer_installer.exe 
```

Enter fullscreen mode Exit fullscreen mode

这将运行。exe 使用葡萄酒。如果它是一个安装程序，那么它应该像在 Windows 中一样运行。如果应用程序要求提供安装应用程序的目录，请选择将其放在 C:\Program Files 下。

为了测试这一点，你可以在这里安装窗口的 Balsamiq 模型创建器。使用上面的指令会变成
*`cd /home/user/Desktop`*

如果文件下载到那个 dir，那么运行:

```
wine Balsamiq_Mockups_3.5.15.exe 
```

Enter fullscreen mode Exit fullscreen mode

这将安装 Balsamiq，安装后。启动 Balsamiq app，开始使用！

## 使用 Wine 启动/运行 Windows 程序

按照上述说明安装应用程序后，可以通过输入葡萄酒 programname.exe(例如`wine realplayer.exe`)来启动和使用这些应用程序。完成后，像平常一样关闭应用程序。您必须在`~/.wine/drive_c`运行已安装的可执行文件，该文件默认位于 Wine 创建的虚拟 Windows 驱动器中。一般来说，程序会按照 Windows 惯例，将自己安装在虚拟 Windows 驱动器中程序文件下的某个位置。

您也可以通过在终端中运行 Wine file 来使用 Wine 文件浏览器。单击工具栏中的 C:\按钮将打开一个窗口，您可以在其中浏览在. wine 中创建的虚拟 Windows 驱动器。双击 Wine 文件浏览器中的可执行文件将在 Wine 中运行它。

您也可以创建一个桌面图标，并使用该图标启动 Wine 应用程序，而不必总是进入终端或使用 Wine 文件浏览器。为此，右击桌面并选择“创建启动器”如果您愿意，从可用图标列表中选择一个图标(或浏览到您想要使用的图标)，填写要求的其他信息(名称、通用名称等)。).对于该命令，键入葡萄酒 the-location-of-the-program.exe(如 *`wine /home/john/.wine/realplayer.exe`* )。创建启动器最重要的部分是命令，通用名称并不重要。请确保您取消选择“在终端中运行”这就完成了这个过程。

在某些情况下，应用程序需要从某个位置运行。在这种情况下，使用命令
创建启动器

```
sh -c "cd /home/USER/.wine/drive_c/Program Files/Appdir/; wine /home/USER/.wine/drive_c/Program Files/Appdir/game.exe" 
```

Enter fullscreen mode Exit fullscreen mode

当然，您需要用正确的数据替换 USER 和 Appdir。如果你想在面板上有一个图标，在选择的面板上创建一个启动器。右键单击面板，选择“添加到面板”，然后选择“自定义应用程序启动器”这将要求您提供与之前相同的信息。

或者，为了让生活更容易，你可以设置它，使葡萄酒会自动打开。exe 文件——而不是每次都使用 Wine 文件来定位文件。为此，右键单击。选择“属性”，然后选择“打开方式”选项卡。单击“添加”按钮，然后单击“使用自定义命令”。在出现的行中，键入 wine，然后点按“添加”,并关闭。现在全部。exe 文件会被 Wine 自动打开，所以可以用 Nautilus 代替 Wine 文件浏览打开。

## 卸载 Wine 应用

打开一个终端窗口，输入下面的命令:

```
wine uninstaller 
```

Enter fullscreen mode Exit fullscreen mode

这将打开一个类似于 Windows 添加/删除程序控制面板的程序，允许您从 Wine 安装中卸载应用程序。直接通过 Wine 运行卸载程序应该也能正常工作。或者，您也可以简单地删除应用程序的文件夹。然而，当在 Windows 中完成时，这种方法将是不干净的，不会像使用卸载程序那样从 Wine 注册表中删除程序的配置。

## 尽情享受！

### **演员表:**

[Winehq 用户指南](https://wiki.winehq.org/Wine_User%27s_Guide#What_is_Wine.3F)
[Ubuntu 社区](https://help.ubuntu.com/community/Wine)
[Papa Google](http://google.com)