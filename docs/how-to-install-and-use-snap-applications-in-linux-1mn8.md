# 如何在 Linux 中安装和使用 Snap 应用程序

> 原文：<https://dev.to/xeroxism/how-to-install-and-use-snap-applications-in-linux-1mn8>

[![install_snap_banner_fossnaija](img/117ddbeb3d60bd766161f4856abd2161.png)T2】](https://i1.wp.com/fossnaija.com/wp-content/uploads/2018/09/install_snap_banner_fossnaija.png?ssl=1)

[Snaps 应用](https://dev.to/xeroxism/what-is-snap-the-raving-new-way-software-is-distributed-in-linux-1mho-temp-slug-1537777) (snaps)是使用 snap 软件包分发系统捆绑的应用；这是由 canonical(开发 ubuntu Linux 发行版的公司)开发的。它已经成为一些流行的桌面应用程序安装在 ubuntu 上的主要方式。许多其他 Linux 发行版现在也支持 snap 包。

# **【恶龙】恶龙(snapd):**

在快照可以在任何 [Linux](https://dev.to/xeroxism/why-linux-works-d6b-temp-slug-8682900) 系统上使用之前，必须首先安装 snapd(快照守护程序)。Snapd 可以安装在许多流行的 Linux 发行版中，如下所示；

## **Ubuntu:**

Snapd 默认安装在 [ubuntu](https://dev.to/xeroxism/ubuntu-linux-is-everywhere-and-connecting-everything-3dm3) 16.04 中，所以你可以立即开始安装应用。

对于较旧的 14.04 LTS(可信)版本或任何默认情况下不包含 snapd 的版本(如 Lubuntu)，您必须从归档中手动安装:

```
 sudo apt update sudo apt install snapd 
```

## 软呢帽:

您可以使用:
安装 snapd 软件包

```
sudo dnf install snapd 
```

使用传统限制的快照，如代码编辑器，也需要一个从/var/lib/snapd/snap 到/snap:
的符号链接

```
sudo ln -s /var/lib/snapd/snap /snap 
```

## Arch Linux:

运行以下命令，使用雅考特·AUR 助手安装软件包:

```
 yaourt -S snapd 
```

安装后，手动启用负责管理 snapd 主通信插座的 systemd 单元:

```
sudo systemctl enable --now snapd.socket 
```

重新启动计算机以使安装后的更改生效。

点击这里了解如何在其他 Linux 发行版中安装它。

# 查找并安装快照

一旦 snapd 安装在您的系统中，您就可以像这样搜索快照:

```
snap find <app_name or description> 
```

该命令会搜索与“电子邮件”相关的所有快照。如果使用的命令没有任何描述或快照名称；返回了一些流行的快照:

[![snap_find_command_result](img/c903a41af40f7692e19c93b5e385d7a6.png)T2】](https://i0.wp.com/fossnaija.com/wp-content/uploads/2018/09/snap_find_command_result.png?ssl=1)

快照存储包含公共和私有快照。

您可以使用命令行安装快照，如下所示:

```
snap install <package_name> 
```

或者，如果您喜欢使用图形界面，请前往 snaps 商店或由 [Brian Douglass](https://uappexplorer.com/snaps) 维护的 snap 应用程序库，只需点击一个按钮即可安装 snap，或者下载 snap 包自行安装。

# 查看已安装的快照

要查看系统上安装的所有快照，请使用 snap list 命令。该命令还提供有关软件版本、修订号、开发人员的信息，以及快照附带的任何额外注释。

[![snap_list_command](img/235c198c8e99400c23ebdd9f90ca6483.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2018/09/snap_find_command_result-copy.png?ssl=1)

# 删除快照

要从系统中删除任何快照应用程序，请使用命令:

```
snap remove <snap_name> 
```

默认情况下，所有快照修订都被删除，包括它们的数据和公共数据目录。

如需完整的快照命令[，请点击此处](https://docs.snapcraft.io/reference/snap-command)。

快乐的 Linux！

帖子[如何在 Linux 中安装和使用 Snap 应用](https://fossnaija.com/how-to-install-and-use-snap-applications-in-linux/)最早出现在 [Foss Naija](https://fossnaija.com) 上。