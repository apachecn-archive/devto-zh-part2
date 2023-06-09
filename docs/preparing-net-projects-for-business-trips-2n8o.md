# 正在准备。商务旅行的净项目

> 原文：<https://dev.to/seankilleen/preparing-net-projects-for-business-trips-2n8o>

我很幸运能偶尔进行一些有趣的商务旅行。然而，没有什么商务旅行的感觉比中途意识到你忘记了为你要做的项目做最新的变更更糟糕了。

下面是我用一些方便的 Powershell 脚本开发的一些步骤，用来在旅行时准备我的代码。

## 定义一些变量

我从两个变量定义开始:

```
$RepoBaseFolder  =  "C:\Users\SeanK\Repositories"  $OfflineNugetLocation  =  "C:\Users\SeanK\OneDrive\Nuget_Offline" 
```

Enter fullscreen mode Exit fullscreen mode

## 更新所有储存库

我将我所有的库存储在`C:\users\SeanK\Repositories`中，我使用 git 来管理 99%的源代码。因此，首先，我提取我的`Repositories`位置中的所有文件夹，并获取每个文件夹的最新信息。

```
Get-ChildItem  C:\Users\SeanK\Repositories\  -Directory  |  Foreach-Object  {  cd  $_.FullName;  git  pull  } 
```

Enter fullscreen mode Exit fullscreen mode

## 拉取并更新 Nuget 包

这确保了我不会遇到任何恢复问题。

我在我的存储库文件夹中找到每个`.sln`文件的位置，并从该目录运行`nuget install`。

```
Get-ChildItem  -Path  C:\Users\SeanK\Repositories  -Recurse  -Include  *.sln  |  Where-Object  {  $_.Directory.FullName  -NotLike  "*node_modules*"  }  |  |  Foreach-Object  {  cd  $_.Directory.FullName;  nuget  install  } 
```

Enter fullscreen mode Exit fullscreen mode

## 拉取和更新 npm 包

同样，更新我所有的 npm 包可能是有意义的——尽管我讨厌所有可能要消耗的 GB:

```
Get-ChildItem  C:\Users\SeanK\Repositories  -Recurse  -Include  package.json  |  Where-Object  {  $_.Directory.FullName  -NotLike  "*node_modules*"  }  |  Foreach-Object  {  cd  $_.Directory.FullName;  npm  install  } 
```

Enter fullscreen mode Exit fullscreen mode

注意对`-NotLike "*node_modules*"`的额外调用。如果我们在`node_modules`内对每个组件进行 npm 安装，事情会比预期的时间长一点。

## 将所有 Nuget 包移动到离线 Nuget 文件夹

好了，我已经为我现有的项目安装了 Nuget 包，这很好。但是如果我想开始一个新项目呢？我怎样才能做到这一点，而不用从网上下载一大堆东西？

我将 Nuget 包复制到我的离线包文件夹中，这恰好也是 OneDrive 文件夹(因此它可以在我的机器上同步)。

```
Get-ChildItem  -Path  $RepoBaseFolder  -Recurse  -Directory  |  Where-Object  {  $_.FullName  -NotLike  "*node_modules*"  -and$_.FullName  -like  "*packages"  }  |  Foreach-Object  {  Copy-Item  $($_.FullName+"\*.*")  $OfflineNugetLocation  -ErrorVariable  capturedErrors  -ErrorAction  SilentlyContinue;  $capturedErrors  |  foreach-object  {  if  ($_  -notmatch  "already exists")  {  write-error  $_  }}} 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意一些事情:

*   对`-NotLike "*node_modules*"`的附加调用。
*   我们还确保只查看以`packages`结尾的路径
*   我们将错误放入一个`ErrorVariable`中，这样我们就可以在复制时抑制“已经存在”的错误

## 启用 NPM 线下包店

有些人使用软件包工具，如 [npm_lazy](https://github.com/mixu/npm_lazy) 或 [local-npm](https://github.com/nolanlawson/local-npm) ，但是[根据文档](https://docs.npmjs.com/cli/cache)，npm 缓存在我的本地目录`%AppData%/npm-cache`中，这意味着如果我需要，我应该能够足够容易地恢复。

因此，只要我提前恢复所有内容，我就不需要对此采取任何实际行动。

## 启用 Nuget 离线包源

如果需要，我会添加一个离线包源(只需要做一次):

`nuget source add -Name "Local offline" -Source C:\Users\SeanK\OneDrive\Nuget_Offline`

当我在 Visual Studio 中时，为了确保脱机源代码是唯一正在使用的源代码，我执行了以下操作:

*   打开 Visual Studio
*   从菜单中选择`Tools`–>`Nuget Package Manager`–>–`Package Manager Settings`
*   选择`Package Manager Sources`
*   取消选择所有不属于您离线来源的可用软件包来源

## 综合起来:剧本

完整的脚本可以在下面找到——你可以随意修改上面的变量，并另存为你自己的`.ps1`文件。

```
$RepoBaseFolder  =  "C:\Users\SeanK\Repositories\"  $OfflineNugetLocation  =  "C:\Users\SeanK\OneDrive\Nuget_Offline"  Get-ChildItem  $RepoBaseFolder  -Directory  |  Foreach-Object  {  cd  $_.FullName;  git  pull  }  Get-ChildItem  -Path  $RepoBaseFolder  -Recurse  -Include  *.sln  |  Where-Object  {  $_.Directory.FullName  -NotLike  "*node_modules*"  }  |  Foreach-Object  {  cd  $_.Directory.FullName;  nuget  install  }  Get-ChildItem  $RepoBaseFolder  -Recurse  -Include  package.json  |  Where-Object  {  $_.Directory.FullName  -NotLike  "*node_modules*"  }  |  Foreach-Object  {  cd  $_.Directory.FullName;  npm  install  }  Get-ChildItem  -Path  $RepoBaseFolder  -Recurse  -Directory  |  Where-Object  {  $_.FullName  -NotLike  "*node_modules*"  -and$_.FullName  -like  "*packages"  }  |  Foreach-Object  {  Copy-Item  $($_.FullName+"\*.*")  $OfflineNugetLocation  -ErrorVariable  capturedErrors  -ErrorAction  SilentlyContinue;  $capturedErrors  |  foreach-object  {  if  ($_  -notmatch  "already exists")  {  write-error  $_  }}}  nuget  source  add  -Name  "Local offline"  -Source  $OfflineNugetLocation 
```

Enter fullscreen mode Exit fullscreen mode

## 你有什么步骤？

我错过了你准备的步骤了吗？您还采取了哪些步骤来准备旅行代码？请在评论中发表意见！

我希望这个教程对你有用。旅途愉快！

[准备中。《商务旅行网项目》](https://SeanKilleen.com/2018/08/preparing-dotnet-business-trip/)最初由肖恩·基林于 2018 年 8 月 21 日在 SeanKilleen.com发表。