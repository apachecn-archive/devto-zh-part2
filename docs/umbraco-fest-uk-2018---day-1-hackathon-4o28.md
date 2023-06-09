# 英国 um braco Fest 2018-第一天:黑客马拉松

> 原文：<https://dev.to/dotnetcoreblog/umbraco-fest-uk-2018---day-1-hackathon-4o28>

声明:随着时间的推移，这将是一篇伪直播博文。

### 14:26

我在下面的积木上有点小麻烦，这让我很挠头。一、Powershell 的块头:

```
$source = "https://dist.nuget.org/win-x86-commandline/latest/nuget.exe"
if ((test-path $nuget) -and ((ls $nuget).CreationTime -lt [DateTime]::Now.AddDays(-$cache)))
{
  Remove-File $nuget
}
if (-not (test-path $nuget))
{
  Write-Host "Download NuGet..."
  Invoke-WebRequest $source -OutFile $nuget
} 
```

上述代码块本身没有任何问题。除了下载的二进制文件(根据 nuget 上的[列表，它是 x86 二进制文件的 4.7.1 版)与我的 Windows 10 虚拟机的架构不兼容:](https://www.nuget.org/downloads/)

```
Program 'nuget.exe' failed to run: The specified executable is not a valid application for this OS platform 
```

我猜这是因为我使用 Azure VM 来构建和测试我的 Powershell 代码。

有意思。

### 13:20

今天早上，我完全忘记开始了。这是我正在做的事情:

构建脚本使用了一堆部分硬编码的路径分隔符(大多数使用 Windows 的开发人员将此称为`\`字符——如果您使用的是基于拉丁字符集的文化)。像这样的东西:

```
$filepath = "$($uenv.SolutionRoot)\src\SolutionInfo.cs" 
```

> 这有什么不好？

好问题。本质上，不同的文化*可以*使用不同的分隔符。例如，一些日本系统使用日元字符(即`¥`)作为路径分隔符。

我对上述代码块的“修正”是使用如下代码:

```
$filepath = [System.IO.Path]::Combine($uenv.SolutionRoot, "src, "SolutionInfo.cs") 
```

我这样做有两个原因:

1.  构建脚本都是用 Powershell 编写的
2.  Powershell 是建立在。网，并允许我们拉。NET APIs 在

另一个额外的好处是，当 Umbraco 转换为时，如果为~~。NET 核心，他们将不必担心跨不同操作系统的不同文件路径分隔符~~

*即基于 Unix 和类 Unix 的操作系统使用`/`，而 Windows 使用`\`(除了上面我说过的地方)*