# 。windows 中的 bashrc 替代方案

> 原文：<https://dev.to/pranay_rauthu/bashrc-alternative-in-windows-kib>

*。bashrc* 是 Linux 环境中非常有用的文件。基本上每次打开一个新的 Linux 外壳时它都会运行&你可以添加别名之类的命令。我想在 windows 中有类似的东西。在互联网上搜索后，我发现了以下技巧。

*   在目录中用命令名创建一个. bat 文件。(例如，如果您想要使用`ls`来列出目录，请在*中创建一个名为 *ls.bat* 的文件 C:\myBatCommands* 并在其中包含`dir`。)
*   将文件夹( *C:\myBatCommands* )添加到 *PATH* 环境变量中
*   您可以开始使用这些命令。

这看起来很简单，但是每次你想添加一个命令时，你需要在文件夹中创建一个新的文件名。因此，创建一个. bat 文件(类似于 *cmdrc.bat* )用下面的内容来简化它。

```
 @echo off

set filename=%~1
set command=%~2

echo %command% > C:\myBatCommands\%filename%.bat 
```

Enter fullscreen mode Exit fullscreen mode

任何时候你想添加一个命令到文件夹，运行下面的命令。

```
 #cmdrc <alias-name> <command>
cmdrc ls dir 
```

Enter fullscreen mode Exit fullscreen mode

这里列出了几个其他的方式[。如果有任何其他方法可以达到同样的效果，请在评论中发表。](https://superuser.com/questions/144347/is-there-windows-equivalent-to-the-bashrc-file-in-linux?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)

编辑:2018 年 4 月 11 日

如果您使用的是 powershell，您可以将 profile.ps1 与所有别名一起使用。更多[信息](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-6)。