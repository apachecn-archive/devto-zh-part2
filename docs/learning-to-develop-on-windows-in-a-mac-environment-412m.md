# 学习在 Mac 环境下进行 Windows 开发

> 原文：<https://dev.to/daveschuetz/learning-to-develop-on-windows-in-a-mac-environment-412m>

我最近完成了一个 web 开发训练营。这种情况并不少见，使用 Windows 机器也不例外。不过，在我的例子中，我是在以 Mac 为中心的环境中使用 Windows 机器。所有安装程序的说明都是在假设每个人都将使用 Mac 的情况下完成的。大多数情况下，教师使用 Mac 来完成他们的工作。因此，在进行故障排除时，我几乎是一个人。这就是为什么我决定写下我的经历，加上教师的建议，这样他们就可以让下一个 Windows 用户去某个地方帮助他们解决问题。这是专门针对大会 Web 开发沉浸式程序编写的。

我的背景一直是 Windows。在开始训练营之前，我在技术支持部门工作，这几乎完全集中在 Windows 上，同时也加入了一些 Linux。这项工作有时需要在 Windows 和 Linux 上都使用命令行，所以我了解文件系统。我也是一个经验丰富的 PC 游戏玩家，多年来已经建造了六台计算机，更不用说无数次更换零件了。凭借我在故障排除方面的丰富经验，我相信使用 Windows 对我来说没问题。

首先，我将谈谈我决定使用的允许使用 Linux 命令的应用程序，然后我将介绍本课程所需的应用程序，最后我将介绍我在本课程中遇到的一些其他问题。这些信息要求您使用 Windows 10，因为我们要完成的一些任务只能在该操作系统上完成。

## 命令行提示

首先，您需要养成从管理命令提示符运行一切的习惯。为此，右键单击命令提示符，将鼠标悬停在“更多”上，然后选择“以管理员身份运行”。另一个选项是从开始菜单中搜索 cmd，按 CTRL+SHIFT+ENTER。您不需要这样做就可以执行一些任务，但是其他任务需要管理权限。

## 安装 Linux

我在研究中发现的第一件事是，你可以在你的机器上安装一个 Linux 子系统。这不是双重引导，也不具备完整的 Linux 发行版的功能，但足以帮助您完成课程。这被称为 Linux 的 Windows 子系统，或 WSL。我们将使用微软[文档](https://docs.microsoft.com/en-us/windows/wsl/install-win10)来安装它。

打开 Windows PowerShell，您需要在安装任何 Linux 发行版之前启用该功能。
在开始菜单中，键入 PowerShell，然后右击它，以管理员身份运行
从 PowerShell 中，复制并粘贴命令
Enable-Windows optional feature-Online-feature name Microsoft-Windows-Subsystem-Linux

如果您没有以管理员身份运行，此命令将会失败，告诉您所请求的操作需要提升权限。这也可以通过控制面板中的程序和功能来完成。然后单击打开或关闭 Windows 功能子菜单。然后找到用于 Linux 的 Windows 子系统，并单击复选框。
您需要重启电脑。

[![WSL Install](img/b673e51fdb020964ba5b3b1f9211c510.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gZ7pjguM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/PxFY6dG.png)

在商店里搜索 Linux。应该会有一个带你去 Linux 应用程序的横幅。
选择你想要的任何一个发行版，我选择了 Ubuntu。
安装完成后启动应用程序。
您将被要求输入用户名和密码。这就是安装 Linux 所需要的一切

正如我前面所说的，这不是一个完整的 Linux 安装，但是这将允许你在你的 Windows 机器上非常无缝地运行一些 Linux 软件。通过在命令前面加上 WSL，您还可以从 Windows 命令行运行一些命令。例如，pwd 是显示当前目录的 Ubuntu 命令，它不是一个有效的 Windows 命令，但是如果你使用 wsl pwd，它会显示目录，从 Linux 根目录。

Linux 文件位于您的配置文件路径的目录中。您可以不通过 Linux 访问这个目录，但不建议这样做，因为如果您不安全，可能会导致操作系统出现问题。也就是说，这可能是必要的。如果你有一个. js 或者。html 文件，您可以从 Windows 或 Linux 打开它。如果你试图从 USB 驱动器中获取一些东西到 Linux 文件中，该怎么办？你需要能够进入 Windows 文件系统。
从 Windows 获取 Linux 文件*C:\ Users \ NAME \ AppData \ Local \ Packages \ canonical group limited。UbuntuonWindows _ 79 rhkp 1 fndgsc \ LocalState \ rootfs*T3】要从 Linux 获取 Windows 文件，需要转到根目录下的 mnt 目录。在中，将有位于计算机上的驱动器，使用由 Windows */mnt/* 分配的它们的字母关联。

虽然这是一个非常有用的特性，但我并没有怎么使用它。这是因为 Windows 不支持从 Windows 使用 Linux 图形应用程序，所以有很多文件系统的移动。如果您想运行 Linux 图形应用程序，您可以做一些事情。你可以从查看下面的链接开始。也就是说，我仍然会安装它，因为它对课程后面的一些事情有帮助。

## Git for Windows

更有用的产品是 Git for Windows。这提供了 BASH 模拟，允许从命令行运行 Git 以及一些更常见的 Linux 命令。转到 [Git for Windows](https://gitforwindows.org/) 网站并下载安装程序。
运行安装程序，选择默认设置。当您开始调整路径环境时，在 Windows 命令提示符下选择使用 Git 和可选 UNIX 工具的选项。

这将允许您使用默认的 Windows 命令提示符来完成本课程中的大部分工作。它将常见的 Linux 命令(pwd、ls、touch 等)添加为可识别的 Windows 命令。PATH 是一个环境变量，它允许命令提示符打开一个程序，而无需键入完整的路径。例如，要从命令提示符打开 VS 代码，您可以只键入-code，否则，您需要键入可执行文件的完整路径。这不会是我们最后一次看到这种赘述，事实上我们会在以后手动添加一些东西。剩下的部分用缺省值完成安装。如果命令提示符处于打开状态，您需要将其关闭，以便识别新命令。

WSL 和 Git for Windows 是本课程中必不可少的程序。接下来，我们将检查本课程所需的一些程序的安装。

## 安装节

Installfest 是安装该类所需的所有应用程序的任务。有一个要运行的命令将连接到学校服务器，该服务器将安装所有需要的程序，但这样写的原因还是因为这个命令只对 Mac 用户有用。你将会想要跟随 Linux 链接，这是因为这仅仅是你将需要安装的一个列表。从该列表中，您可以从各自的网站下载安装文件。可能有多个程序想要重新启动计算机，安全的做法是等到所有程序都安装完毕后再重新启动一次。安装完所有程序后，您可以返回默认的 Installfest 页面，按照 GitHub 的安装和配置步骤进行操作。

## 代码编辑器— VS 代码

您将需要从代码编辑器开始。指导老师会推荐 VS Code 或者 Atom，但是如果你已经有了一个喜欢的编辑器，你会推荐你的。我选择了 VS 代码。你可以从他们的[网站](https://code.visualstudio.com/docs/setup/windows)下载，文档链接在顶部，然后设置链接在侧面。你可以按照默认的安装。这包括保留复选框以添加到路径中。

## Node.js

接下来是 [Node.js](https://nodejs.org/en/) ，同样可以从他们的网站下载。这也可以直接通过缺省值来完成。您将再次希望确保为 NPM 和节点安装路径变量。这将是一个默认，所以你不应该做任何事情。

## MongoDB

还是那句话，去网站安装 [MongoDB](https://docs.mongodb.com/master/tutorial/install-mongodb-on-windows/#install-mdb-edition) for Windows。运行安装程序，选择完整安装。保留默认配置。这将允许我们启动和停止 MongoDB 服务。然后单击安装。
最后，让我们将变量添加到路径环境中。您可以右键单击桌面上的计算机图标，选择属性，然后转到高级。或者，您可以从开始菜单中搜索环境，并从控制面板中选择编辑。

在对话框中，单击环境变量。这可以作为系统变量，用于登录到计算机的每个用户，也可以作为用户变量，仅用于登录的用户。找到路径，然后单击编辑。单击 new，添加 C:\ Program Files \ MongoDB \ Server \ 4.0 \ bin(如果 4.0 不是当前版本，请务必更改您的版本号以匹配)，然后单击 OK。这个设置将允许您使用快速命令来启动和进入 MongoDB。

[![environmental variable](img/a8b4fe7f73647025352d0674cb483e5c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mFw1tgYE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/DLV3Mmh.png)

## 巨蟒

从网站获取 Python 安装程序。确保添加到窗口底部的 PATH，然后单击 install，这将安装 Python。在撰写本文时，这个安装是 Python 3。在本课程中使用 python 时，所使用的一些命令会以 python3 和 pip3 的形式给出，如果您的计算机上只有我们安装的版本，您只需使用 Python 和 pip 命令。Pip 是 python 的包安装程序。例如，在某个时候，您将要安装 ipython，您将使用 **pip install ipython** 来安装它。

## PostgreSQL

从 Installfest 最后安装的是 [PostgreSQL](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads) 。从网站获取安装文件，并遵循默认安装。您还需要再次修改环境路径。再次遵循 MongoDB 部分的指示。然后添加新路径*C:\ Program Files \ PostgreSQL \ 11 \ bin*(确保版本正确，因为当前写的是 11)。
在安装过程中，我们为 postgres 用户创建了一个密码，这是默认的超级用户，我们没有办法更改用户名。现在，当我们进入 cmd 并尝试打开数据库时，它会要求您输入用户名的密码。这将导致登录错误，因为该用户没有被设置为数据库的用户。我们可以做一些事情来解决这个问题，首先，您可以将用户添加为超级用户。这需要在使用 postgres 登录时执行该命令，或者使用 PG Admin 图形工具。我不会重复这些，但是如果这是你想走的路，那就走吧。最简单的方法是在启动数据库时使用 user 属性 **psql -U postgres** 。最后一种方式，我觉得是最好的方式，就是把它作为环境变量加入。再次进入环境变量属性，这次不是编辑路径，而是创建一个新变量。这可以在用户或系统变量中完成，点击新建，在变量名中键入 *PGUSER* 和变量值为 *postgres* 。现在，启动数据库所需要做的就是在命令提示符下键入 **psql** ，然后它会询问 postgres 密码。

现在我们所有的主要程序都已经安装好了，一些设置也完成了，我们将回顾一下我在 12 周内遇到的其他一些问题，以及我是如何解决的。

## 其他问题

与 Windows 命令相比，您可能首先会遇到 Mac/Linux 命令的问题。随着 Git BASH 的安装，这应该不是问题。一般来说，你可以很容易地搜索到类似的命令，我也添加了一个[链接](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Step_by_Step_Guide/ap-doslinux.html)到一些常见的命令。

在整个课程中，会有一些检查点来测试你对材料的了解。这些通常是您必须运行测试的编码挑战。这让我非常头疼。这些测试都是在考虑 Mac 的情况下编写的，所以会遇到一些问题。首先，Mac 和 Windows 路径不遵循相同的约定。这就是 WSL 非常有用的地方，因为 Mac 路径约定遵循与 Linux 相同的标准。您将希望使用您安装的 Linux BASH，并遵循上面关于如何导航到 Windows 文件夹来执行您的测试的说明。也就是说，我遇到的另一个问题是，即使使用 WSL，测试也不会针对我们采用的第一个检查点运行。我和老师们能想出的最好的办法是，这与意想不到的语法问题有关。唯一要做的事情就是写代码，然后抱最好的希望。在其他任何检查点都没有出现这个问题。

我遇到的最后一个小问题是在课程的 Python 和 Django 部分。使用 Python 时，最佳实践是使用虚拟环境。使用**pip install virtualenvwrapper-win**安装包。然后你将使用**mkvirtualenv*env name*T5】创建环境。这将自动激活环境，但如果您需要再次启动环境，请使用 **workon *envname*** 。**

尽管这些信息中有很多对任何 Windows 机器上的初学者都是有用的，但正如我之前所说的，重点是我所学的具体课程。在你寻找解决方案的时候，遵循这些指示有望让你不至于落后。也有一些好的链接，你可能也想看看。祝你好运。

## 链接

[https://www . how togeek . com/265900/everything-you-can-do-with-windows-10s-new-bash-shell/](https://www.howtogeek.com/265900/everything-you-can-do-with-windows-10s-new-bash-shell/)
[https://www . how togeek . com/261575/how-to-run-graphical-Linux-desktop-applications-from-windows-10s-bash-shell/](https://www.howtogeek.com/261575/how-to-run-graphical-linux-desktop-applications-from-windows-10s-bash-shell/) [https://www . how togeek . com/249966/how-install](https://www.howtogeek.com/249966/how-to-install-and-use-the-linux-bash-shell-on-windows-10/)

### 安装链接

[https://docs.microsoft.com/en-us/windows/wsl/install-win10](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
[https://gitforwindows.org/](https://gitforwindows.org/)
[https://code.visualstudio.com/docs/setup/windows](https://code.visualstudio.com/docs/setup/windows)
[https://nodejs.org/en/](https://nodejs.org/en/)
T12】https://docs . MongoDB . com/master/tutorial/install-MongoDB-on-windows/# install-MDB-edition
[https://www.python.org/downloads/](https://www.python.org/downloads/)
[https://www . enterprise db . com/downloads/postgres-PostgreSQL-downloads](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)