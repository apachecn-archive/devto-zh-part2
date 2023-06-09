# 新立得软件包管理器如何在 Ubuntu Linux 中安装新立得软件包管理器

> 原文：<https://dev.to/xeroxism/how-to-install-synaptic-package-manager-in-ubuntu-linux-hm3>

[![synaptic_banner](img/df720ef56c62f0af8997bf923aef416c.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2018/08/synaptic_banner.png?ssl=1)

新立得是一个 apt 的图形化包管理程序。它提供了与 apt-get 命令行实用程序相同的功能，具有基于 Gtk+的 GUI 前端。如果你更喜欢在 Debian 和基于 Debian 的系统中使用 GUI 安装应用程序，那么 synaptic 将是值得的。

[![synaptic_screenshot_1](img/b80a22d5180db8d135096454815c1f9c.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2018/08/synaptic_screenshot_1.png?ssl=1)

## 特性:

*   升级你的整个系统。
*   管理包存储库(sources.list)。
*   通过名称、描述和其他一些属性来查找包。
*   按状态、部分、名称或自定义过滤器选择包。
*   按名称、状态、大小或版本对包进行分类。
*   浏览与软件包相关的所有可用在线文档。
*   下载软件包的最新变更日志。
*   将包锁定到当前版本。
*   强制安装特定的软件包版本。
*   撤消/重做选择。
*   包管理器的内置终端模拟器。
*   仅 Debian/Ubuntu:通过 debconf 系统配置软件包。| |

[![synaptic_screenshot_3](img/dd8773457e0a748d000ae6d58223e1bd.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2018/08/synaptic_screenshot_3.png?ssl=1)

# 如何安装:

在命令[终端](https://dev.to/xeroxism/6-commands-you-must-know-to-be-productive-on-the-terminal-h9a)(快捷键= > Ctrl+Alt+T)上，输入:

```
sudo apt-get update

sudo apt-get install synaptic 
```

Enter fullscreen mode Exit fullscreen mode

这里用的是 Ubuntu 16.04 (Xenial)。您可以获取二进制文件(。deb)包从[这里](https://packages.ubuntu.com/xenial/synaptic)。

安装后，你现在可以使用新立得在你的系统中安装/升级/删除任何你想要的软件包。使用搜索功能，你只需输入你想安装的软件包的名称，然后新立得会为你搜索并安装它及其附件。

快乐的 Linux！

帖子[如何在 Ubuntu Linux 中安装 Synaptic Package Manager](https://fossnaija.com/how-to-install-synaptic-package-manager-in-ubuntu-linux/)最早出现在 [Foss Naija](https://fossnaija.com) 上。