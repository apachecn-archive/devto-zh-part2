# 集成自动化

> 原文：<https://dev.to/prelias/automatizao-de-intalaes-b2f>

[![](img/ebf27be9142a8b18a4a0470b6937b838.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PKSTTxC4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.especializati.com.br/wp-content/uploads/2017/05/chocolatey.png)

随着 Windows 迁移到商店格式的应用程序，对于所有类型的用户和软件来说，我今天的提示可能不再有趣。但是，许多应用程序尚未分发，或者可能永远不会分发。

并且一个对这项任务有很大帮助的软件是巧克力。该应用程序连接到存储库，用户可以在其中创建与各种工具的下载目录相关的脚本。用户自己会维护这些脚本，但他们在尝试避免恶意文件和代码时会受到限制，因此可以说，通过他们安装应用程序就像通过常规下载一样安全。

在此，我开发了一个脚本[【powershell】](https://docs.microsoft.com/pt-br/powershell/scripting/getting-started/getting-started-with-windows-powershell?view=powershell-6)，它下载自己的巧克力，然后用它安静地安装我经常使用的所有应用程序:

上面的脚本使用“chocolate upgrade”命令，因为它在找不到时安装；如果已经安装了 chocolate upgrade 命令，则执行升级；也就是说，您可以使用同一脚本进行将来的升级。

如果您愿意，可以通过单击“”查看目录中的 chocolate 软件包等可用软件，并根据需要自定义我的脚本。您还可以在此链接中查看每个安装脚本的详细信息，以了解文件和所有命令的下载位置。

只需下载文件(单击 post 窗口底部的文件名)，或复制文件内容并使用 Powershell 使用的 ps1 扩展名进行保存(您可以单击“”按钮在屏幕上仅查看其内容)。

另一个相关提示是使用文件修改注册表。在 Windows 上下文菜单(右键单击)中提供了以管理员身份直接运行 Powershell 脚本的选项。

只需运行文件(与建议的第一个 post 文件相同)并接受注册表修改即可。

腹肌。