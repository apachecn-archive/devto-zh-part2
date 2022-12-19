# 简单，自动化的代码覆盖。网络核心

> 原文：<https://dev.to/nlowe/easy-automated-code-coverage-for-net-core-1khh>

对于我的项目，我喜欢使用 [TravisCI](https://travis-ci.org/) 来运行我的构建和测试。如果我正在使用一种支持它的语言，我也喜欢向[发布测试运行的代码覆盖结果。不久前，我发现了一个跨平台的工具来计算覆盖率的结果。净核心项目:](https://coveralls.io/)

## ![GitHub logo](../Images/04febf72106d337f3956ebfaf9121dae.png)[【lucaslorntz】](https://github.com/lucaslorentz)/[【微型汽车】](https://github.com/lucaslorentz/minicover)

### 跨平台代码覆盖工具。网络核心

<article class="markdown-body entry-content container-lg" itemprop="text">

# 迷你封面

的代码覆盖率工具。网络核心

[![Build Status](../Images/3f39f48bf651224f070499477474767f.png)](https://dev.azure.com/lucaslorentzlara/lucaslorentzlara/_build/latest?definitionId=3&branchName=master)[![Nuget](../Images/1041aa7e063c4dc1a81ced1408af5f28.png)](https://www.nuget.org/packages/MiniCover/)[![Coverage Status](../Images/34d10a22fa7625a27713815d08a3bd4b.png)](https://coveralls.io/github/lucaslorentz/minicover?branch=master)

## 支持。NET Core SDKs

*   2.1(全局工具)
*   2.2(全局工具)
*   3.0(全局工具或局部工具)
*   3.1(全局工具或局部工具)

## 装置

MiniCover 可以作为全局工具安装:

```
dotnet tool install --global minicover 
```

或本地工具:

```
dotnet tool install minicover 
```

## 命令

这是 MiniCover 命令和选项的简化文档。

使用`--help`了解更多信息:

```
minicover --help 
```

**当作为本地工具安装时，MiniCover 命令必须以`dotnet`为前缀。**举例:

```
dotnet minicover --help 
```

### 工具

```
minicover instrument 
```

使用此命令检测程序集以记录代码覆盖率。

它基于以下主要选项:

| 选择权 | 描述 | 类型 | 系统默认值 |
| --- | --- | --- | --- |
| **来源** | 跟踪覆盖率的源文件 | 一团 | `src/**/*.cs` |
| **排除来源** | 来源选项的例外 | 一团 | `**/bin/**/*.cs`和`**/obj/**/*.cs` |
| **测试** | 用于识别测试方法的测试文件 | 一团 | `tests/**/*.cs`和`test/**/*.cs` |
| **排除测试** | 测试选项的例外 | 一团 | `**/bin/**/*.cs`和`**/obj/**/*.cs` |
| **组件** | 装配 |

…</article>

[View on GitHub](https://github.com/lucaslorentz/minicover)

这篇文章的所有示例代码都可以在我的 GitHub 个人资料中找到:

## ![GitHub logo](../Images/04febf72106d337f3956ebfaf9121dae.png)[nlow](https://github.com/nlowe)/[工作服——netcore](https://github.com/nlowe/coveralls-netcore)

### 一个 dotnet 核心应用程序的例子

<article class="markdown-body entry-content container-lg" itemprop="text">

# 工作服 Netcore 示例

[![Build Status](../Images/0f867648549be64c8b2266cbe2b2ddb2.png) ](https://travis-ci.org/nlowe/coveralls-netcore) [ ![Coverage Status](../Images/4a191547decee3137104c004297b5a88.png)](https://coveralls.io/github/nlowe/coveralls-netcore?branch=master)

从一个 travis 构建的 dotnet 核心项目中获取代码覆盖率。

通过 [Cake 使用](https://github.com/nlowe/Cake.MiniCover) [MiniCover](https://github.com/lucaslorentz/minicover) 生成的覆盖率。迷你 Cover

</article>

[View on GitHub](https://github.com/nlowe/coveralls-netcore)

## 使用 MiniCover

MiniCover 作为一个`dotnet` cli 工具发布。我们要么需要将它作为一个`DotNetCliToolReference`添加到回购中的一个顶级项目，要么作为一个专用工具项目。我喜欢把参考放在`./minicover/minicover.csproj`，原因很快就会清楚:

```
<!-- ./minicover/minicover.csproj -->
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
    <IsPackable>false</IsPackable>
  </PropertyGroup>

  <ItemGroup>
    <DotNetCliToolReference Include="MiniCover" Version="2.0.0-ci-20180517205544" />
  </ItemGroup>

</Project> 
```

一定要在这个项目上运行`dotnet restore`。

在 MiniCover 更新到支持作为全局工具安装(在中添加)之前，这是必需的。网络核心 2.1):

# [![GitHub logo](../Images/04febf72106d337f3956ebfaf9121dae.png) 发表为。净芯全球工具 #90](https://github.com/lucaslorentz/minicover/issues/90) 

[![snebjorn avatar](../Images/e65bceadecd51b069dd0c4c0838dd0cd.png) ](https://github.com/snebjorn)  **[snebjorn](https://github.com/snebjorn)** posted on [<time datetime="2018-04-23T12:08:03Z">Apr 23, 2018</time>](https://github.com/lucaslorentz/minicover/issues/90)

随着 dotnet core 2.1 的即将发布，他们正在引入全球工具。它像 npm 全球工具(NPM 安装-g 包)一样工作

这将是安装 MiniCover 的一个很好的替代方法。所以你可以简单地做`dotnet install tool -g minicover`而不是一个工具项目

[View on GitHub](https://github.com/lucaslorentz/minicover/issues/90)

由于它仍在积极开发中，MiniCover 作为预发布包分发。检查 [nuget feed](https://www.nuget.org/packages/MiniCover/) 获取最新版本。

### 生成覆盖率

我们已经准备好运行我们的测试了:

#### 构建项目

较新版本的。NET Core 在这里包含了隐式恢复。如果您使用的是旧版本的。网芯一定要先做一个`dotnet restore`。

```
dotnet build 
```

#### 对被测组件进行测试。

这会将 IL 指令插入到您的程序集中，以将覆盖率写入结果文件:

```
cd minicover
dotnet minicover instrument --workdir ../ --assemblies test/**/bin/**/*.dll --sources src/**/*.cs
dotnet minocover reset
cd .. 
```

#### 运行您的测试:

您必须告诉`dotnet`在运行测试时不要重新构建程序集。否则，我们刚刚添加的工具将被覆盖，并且没有覆盖被跟踪。

```
for project in test/**/*.csproj; do dotnet test --no-build $project; done 
```

#### 生成报表:

MiniCover 支持呈现多种类型的报告。最基本的类型是带有致命阈值的控制台报告。如果覆盖率低于这个阈值，它将会以一个非零的退出代码退出，允许您在覆盖率太低的情况下使构建失败。

```
cd minicover
dotnet minicover report --workdir ../ --threshold 90
cd .. 
```

下面是一个示例(在支持颜色的 TTY 中，高于阈值的文件将是绿色的，低于阈值的文件将是红色的):

```
+-------------------+-------+---------------+------------+
| File              | Lines | Covered Lines | Percentage |
+-------------------+-------+---------------+------------+
| src/MyLib/Math.cs |    2  |        1      |   50.000%  |
+-------------------+-------+---------------+------------+
| All files         |    2  |        1      |   50.000%  |
+-------------------+-------+---------------+------------+ 
```

#### 卸载组件

如果您计划为这个构建发布工件，请确保卸载程序集:

```
cd minicover
dotnet minicover uninstrument --workdir ../
cd .. 
```

## 用蛋糕简化事物

虽然这是获得代码覆盖率结果的一个很好的方法，但是有点乏味。为了我的。NET 项目，我更喜欢使用`cake`来编写构建过程的脚本:

## ![GitHub logo](../Images/04febf72106d337f3956ebfaf9121dae.png) [蛋糕制作](https://github.com/cake-build) / [蛋糕](https://github.com/cake-build/cake)

### Cake (C# Make)是一个跨平台构建自动化系统。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 蛋糕

[![NuGet](../Images/96e252b00a68b2bbba8b4a3a0e06a499.png)](https://www.nuget.org/packages/Cake)[![Azure Artifacts](../Images/f733cfb917fe7523acbf18197dad3c13.png)](https://dev.azure.com/cake-build/Cake/_packaging?_a=package&feed=cake&package=Cake&protocolType=NuGet)[![Chocolatey](../Images/0272d8dd24b385eda4b001768d53fe7f.png)](https://chocolatey.org/packages/cake.portable)[![homebrew](../Images/0fa4769641fd30f488397cd46164230d.png)](http://braumeister.org/formula/cake)[![Help Contribute to Open Source](../Images/5b46366f72f7d1c07a314998f7107652.png)](https://www.codetriage.com/cake-build/cake)

[![Source Browser](../Images/f771f188ef07d8915122abb9c9cba3d5.png)T2】](http://sourcebrowser.io/Browse/cake-build/cake)

Cake (C# Make)是一个构建自动化系统，使用 C# DSL 来做诸如编译代码、复制文件/文件夹、运行单元测试、压缩文件和构建 NuGet 包之类的事情。

## 连续累计

| 构建服务器 | 平台 | 构建状态 | 集成测试 |
| --- | --- | --- | --- |
| 天蓝色管道 | 马科斯 | [![Azure Pipelines Mac Build status](../Images/782d6c781804a4b422ef6d4e4251c9b9.png)T2】](https://dev.azure.com/cake-build/Cake/_build/latest?definitionId=4) |
| 天蓝色管道 | Windows 操作系统 | [![Azure Pipelines Windows Build status](../Images/b35ff16f7b4c494097c4268cf3cf9aa4.png)T2】](https://dev.azure.com/cake-build/Cake/_build/latest?definitionId=1) |
| 天蓝色管道 | 一种自由操作系统 | [![Azure Pipelines Debian Build status](../Images/78988d8a31f46838c90fde75eca32a50.png)T2】](https://dev.azure.com/cake-build/Cake/_build/latest?definitionId=7) |
| 天蓝色管道 | 一种男式软呢帽 | [![Azure Pipelines Fedora Build status](../Images/6de5d849c176168b8155f6ce55d5fb64.png)T2】](https://dev.azure.com/cake-build/Cake/_build/latest?definitionId=6) |
| 天蓝色管道 | 摘录 | [![Azure Pipelines Cake Centos status](../Images/9281f01aa63132e21992dfd8737fa27e.png)T2】](https://dev.azure.com/cake-build/Cake/_build/latest?definitionId=5) |
| 天蓝色管道 | 人的本质 | [![Azure Pipelines Ubuntu Build status](../Images/9a273cbe6f314152d2ba6d572e6fa66f.png)T2】](https://dev.azure.com/cake-build/Cake/_build/latest?definitionId=3) |
| 应用程序供应商 | Windows 操作系统 | [![AppVeyor branch](../Images/bce2237f35ad50bfb14f2556d3af3291.png)T2】](https://ci.appveyor.com/project/cakebuild/cake/branch/develop) | [![AppVeyor branch](../Images/f39722f1ce5885e7774b39a49cb99ddf.png)T2】](https://ci.appveyor.com/project/cakebuild/cake-eijwj) |
| 特拉维斯 | Ubuntu / MacOS | [![Travis build status](../Images/62299f01a3ffc62fccac10655cba4094.png)T2】](https://travis-ci.org/cake-build/cake) |
| TeamCity | Windows 操作系统 | [![TeamCity Build Status](../Images/4e9fe6eb30cb898be6e025f007444b16.png)T2】](http://teamcity.codebetter.com/viewType.html?buildTypeId=Cake_CakeMaster) |
| 按位 | 马科斯 | [![Build Status](../Images/fd6daf018c78397b7bd49732cd10ed33.png)T2】](https://app.bitrise.io/app/42eaef77e8db4a5c) | [![Build Status](../Images/1b9493e0bf5afe72d083ef4bd7732de8.png)T2】](https://camo.githubusercontent.com/bdb72a036eb664fb9d846cdde9ae2fc4d99f84ad/68747470733a2f2f6170702e626974726973652e696f2f6170702f383034623433316331663237653061302f7374617475732e7376673f746f6b656e3d714b6f734845614a414a45717a5a6371347335575267266272616e63683d646576656c6f70) |
| 按位 | 一种自由操作系统 | [![Build Status](../Images/bdcb40a1c9af3e7958a97692bb9a84d8.png)T2】](https://app.bitrise.io/app/ea0c6b3c61eb1e79) | [![Build Status](../Images/6fd0c488b10461af0162ba9fdd8f8a7e.png)T2】](https://camo.githubusercontent.com/64e6f6c3c0337e374584a5e746131af44471864d/68747470733a2f2f6170702e626974726973652e696f2f6170702f356134303666333466323231313363362f7374617475732e7376673f746f6b656e3d54515062736d413979502d694a4f687a756e49503477266272616e63683d646576656c6f70) |
| 比特桶管道 | 一种自由操作系统 | [![Build Status](../Images/9864bc9fe9df22570bf5e13ef99d618c.png)T2】](https://cakebitbucketpipelinesshield.azurewebsites.net/url/cakebuild/cake-integration-tests/develop) |
| GitLab | 一种自由操作系统 | [![pipeline status](../Images/6c15d8c13adc1f936ccd651e45ddcf11.png)T2】](https://gitlab.com/cake-build/cake/commits/develop) |   |
| GitHub 操作 | Windows / Ubuntu/ macOS | [![Build Status](../Images/9bc6c7c53bc03528206f376e39e25bd9.png)T2】](https://github.com/cake-build/cake/actions) |   |

## 代码覆盖率

[![Coverage Status](../Images/9c39a7b2800eb762eab8ca2abef6ab7f.png)T2】](https://coveralls.io/github/cake-build/cake?branch=develop)

## 目录

1.  [文档](https://github.com/cake-build/cake#documentation)
2.  [例子](https://github.com/cake-build/cake#example)
    *   [安装蛋糕引导程序](https://github.com/cake-build/cake#1-install-the-cake-bootstrapper)
    *   [创建蛋糕脚本](https://github.com/cake-build/cake#2-create-a-cake-script)
    *   [跑吧！](https://github.com/cake-build/cake#3-run-it)
3.  [投稿](https://github.com/cake-build/cake#contributing)
4.  [取得联系](https://github.com/cake-build/cake#get-in-touch)
5.  [执照](https://github.com/cake-build/cake#license)

## 证明文件

你可以在 https://cakebuild.net/阅读最新的文档。

## 例子

此示例下载了 Cake 引导程序并执行了一个简单的构建脚本。引导程序用于以简单的方式引导 Cake，而不是执行构建脚本所必需的。如果你…

</article>

[View on GitHub](https://github.com/cake-build/cake)

要开始使用 cake，您需要两个文件，一个`build.cake`构建脚本和一个特定于平台的引导程序脚本。因为我们可以为。NET 核心项目，我们可以包含一个用于 Windows 的 PowerShell 脚本和一个用于其他平台的 Bash 脚本。如果您想开始用 cake 构建. NET 核心项目，请随意使用我的引导程序脚本:

## ![GitHub logo](../Images/04febf72106d337f3956ebfaf9121dae.png)[nlow](https://github.com/nlowe)/[蛋糕-bootstrap-dotnet](https://github.com/nlowe/cake-bootstrap-dotnet)

### 使用 cake 和 dotnet 核心的引导程序脚本

<article class="markdown-body entry-content container-lg" itemprop="text">

# dotnet 核心项目的蛋糕引导程序

dotnet 核心项目的引导蛋糕，无需安装 mono。选项(环境变量):

*   `TOOLS_DIR`:安装蛋糕工具的路径。`./tools`默认情况下
*   `CAKE_VERSION`:要安装的蛋糕版本。`0.26.1`默认情况下。要升级蛋糕，删除你的`TOOLS_DIR`并改变这个变量。
*   `CAKE_NETCOREAPP_VERSION`:用于工具虚拟项目的`netcoreapp`版本。`2.0`默认。必须兼容 [`Cake.CoreCLR`](https://www.nuget.org/packages/Cake.CoreCLR/)

所有其他选项都与标准[引导程序](https://github.com/cake-build/example)脚本一样存在。

</article>

[View on GitHub](https://github.com/nlowe/cake-bootstrap-dotnet)

通过`addin`系统，Cake 具有极强的可扩展性。这将在运行构建脚本之前将扩展恢复为 Cake 的一个`nuget`包。我为 minicover 制作了一个蛋糕插件，它极大地简化了覆盖率生成过程:

## ![GitHub logo](../Images/04febf72106d337f3956ebfaf9121dae.png) [ nlowe ](https://github.com/nlowe) / [蛋糕。迷你 Cover](https://github.com/nlowe/Cake.MiniCover)

### 一个用于 Minicover 的蛋糕插件，使得在 dotnet core 上获得跨平台代码覆盖尽可能容易

<article class="markdown-body entry-content container-lg" itemprop="text">

# 蛋糕。迷你 Cover

[![Build Status](../Images/662ed9648b8585a83279e9d6af5900c1.png) ](https://travis-ci.org/nlowe/Cake.MiniCover) [ ![nuget](../Images/7d7cb5c9ebf14fdfc755af3044f3f933.png)](https://www.nuget.org/packages/Cake.MiniCover/)

一个[蛋糕](https://cakebuild.net)插件给[迷你封面](https://github.com/lucaslorentz/minicover)

## 使用

在解决[lucaslorentz/minicover # 31](https://github.com/lucaslorentz/minicover/issues/31)之前，您需要调用`SetMiniCoverToolsProject`别名来定位工具项目:

```
#addin "Cake.MiniCover"
SetMiniCoverToolsProject("./minicover/minicover.csproj")
// ...
Task("Coverage")
    .IsDependentOn("build")
    .Does(() => 
{
    MiniCover(tool =>
        {
            foreach(var project in GetFiles("./test/**/*.csproj"))
            {
                tool.DotNetCoreTest(project.FullPath, new DotNetCoreTestSettings()
                {
                    // Required to keep instrumentation added by MiniCover
                    NoBuild = true,
                    Configuration = configuration
                });
            }
        },
        new MiniCoverSettings()
            .WithAssembliesMatching("./test/**/*.dll")
            .WithSourcesMatching("./src/**/*.cs")
            .GenerateReport(ReportType.CONSOLE | ReportType.XML)
    );
});

// ...

```

如果您需要更精细的控制或者有多个测试目标，您可以单独调用别名:

```
#addin "Cake.MiniCover"
SetMiniCoverToolsProject
```

…</article>

[View on GitHub](https://github.com/nlowe/Cake.MiniCover)

### 构建脚本

Cake 将构建脚本组织成一系列具有依赖关系的`Task`。插件暴露别名让我们调用。

#### 引用插件

第一项事务是引用我们的 addin:

```
#addin "nuget:?package=Cake.MiniCover&version=0.29.0-next20180721071547&prerelease" 
```

> **注意**:我最近修复了对[https://workalls . io](https://coveralls.io)发布覆盖的支持。现在，你需要参考`Cake.MiniCover`的`0.29.0-next20180721071547`预发布，直到`0.29.0`发布。
> 
> 另外，这个版本的`Cake.MiniCover`需要`Cake` 0.29+。你将需要使用我在本文开头链接的示例回购中的引导脚本，或者更新它们以引用`Cake` 0.29+

#### 定位工具项目

接下来，我们需要告诉 Cake 我们的 MiniCover tools 项目在哪里:

```
SetMiniCoverToolsProject("./minicover/minicover.csproj"); 
```

#### 编写测试目标

我们可以将我们的 [`DotNetCoreTest`](http://cakebuild.net/api/Cake.Common.Tools.DotNetCore/DotNetCoreAliases/8191BBC4) 调用包装在一个 [`MiniCover`](http://cakebuild.net/api/Cake.MiniCover/MiniCoverAliases/6959692A) 调用中。这将自动检测和卸载程序集，并运行我们请求的任何报告:

```
Task("Test")
    .IsDependentOn("Build")
    .Does(() => 
{
    MiniCover(tool => 
        {
            foreach(var project in GetFiles("./test/**/*.csproj"))
            {
                DotNetCoreTest(project.FullPath, new DotNetCoreTestSettings
                {
                    Configuration = configuration,
                    NoRestore = true,
                    NoBuild = true
                });
            }
        },
        new MiniCoverSettings()
            .WithAssembliesMatching("./test/**/*.dll")
            .WithSourcesMatching("./src/**/*.cs")
            .WithNonFatalThreshold()
            .GenerateReport(ReportType.CONSOLE)
    );
}); 
```

为了简洁起见，我在这里只包括了`Test`任务。关于`Build`任务定义和执行该任务所需的其他变量，请参见[完整构建脚本](https://raw.githubusercontent.com/nlowe/coveralls-netcore/master/build.cake)。

注意`WithNonFatalThreshold()`设置。如果覆盖率低于指定的阈值，这将禁止错误退出，如果您希望您的构建系统跟踪构建，但有一个不同的系统来跟踪覆盖率，这是很有用的。

### 生成覆盖结果

现在，我们可以简单地调用我们的构建脚本来运行我们的单元测试，并将覆盖报告打印到控制台:

```
# On macOS / Linux:
./build.sh -t test 
```

或者

```
# Windows:  ./build.ps1  -t  Build 
```

Cake 将计算出任务的依赖关系，并以适当的顺序执行它们，显示输出并跟踪每个任务运行的时间:

```
$ ./build.sh -t test Installing Cake 0.29.0
  Writing /tmp/tmpcaiVOk.tmp
info : Adding PackageReference for package 'Cake.CoreCLR' into project '/home/nathan/projects/coveralls-netcore/tools/cake.csproj'.
log  : Restoring packages for /home/nathan/projects/coveralls-netcore/tools/cake.csproj...
info :   GET https://api.nuget.org/v3-flatcontainer/cake.coreclr/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/cake.coreclr/index.json 135ms
info :   GET https://api.nuget.org/v3-flatcontainer/cake.coreclr/0.29.0/cake.coreclr.0.29.0.nupkg
info :   OK https://api.nuget.org/v3-flatcontainer/cake.coreclr/0.29.0/cake.coreclr.0.29.0.nupkg 49ms
log  : Installing Cake.CoreCLR 0.29.0.
info : Package 'Cake.CoreCLR' is compatible with all the specified frameworks in project '/home/nathan/projects/coveralls-netcore/tools/cake.csproj'.
info : PackageReference for package 'Cake.CoreCLR' version '0.29.0' added to file '/home/nathan/projects/coveralls-netcore/tools/cake.csproj'.
Analyzing build script...
Processing build script...
Installing addins...
Compiling build script...
  Restoring packages for /home/nathan/projects/coveralls-netcore/minicover/minicover.csproj...
  Restore completed in 73.51 ms for /home/nathan/projects/coveralls-netcore/minicover/minicover.csproj.
  Generating MSBuild file /home/nathan/projects/coveralls-netcore/minicover/obj/minicover.csproj.nuget.g.props.
  Generating MSBuild file /home/nathan/projects/coveralls-netcore/minicover/obj/minicover.csproj.nuget.g.targets.
  Restore completed in 180.67 ms for /home/nathan/projects/coveralls-netcore/minicover/minicover.csproj.

========================================
Restore
========================================
Executing task: Restore
  Restoring packages for /home/nathan/projects/coveralls-netcore/test/MyLib.Tests/MyLib.Tests.csproj...
  Restoring packages for /home/nathan/projects/coveralls-netcore/src/MyLib/MyLib.csproj...
  Restore completed in 63.88 ms for /home/nathan/projects/coveralls-netcore/test/MyLib.Tests/MyLib.Tests.csproj.
/home/nathan/projects/coveralls-netcore/src/MyLib/MyLib.csproj : warning NU1603: MyLib depends on NETStandard.Library (>= 2.0.2-servicing-26420-0) but NETStandard.Library 2.0.2-servicing-26420-0 was not found. An approximate best match of NETStandard.Library 2.0.2 was resolved. [/home/nathan/projects/coveralls-netcore/coveralls-netcore.sln]
  Generating MSBuild file /home/nathan/projects/coveralls-netcore/test/MyLib.Tests/obj/MyLib.Tests.csproj.nuget.g.props.
  Generating MSBuild file /home/nathan/projects/coveralls-netcore/src/MyLib/obj/MyLib.csproj.nuget.g.props.
  Generating MSBuild file /home/nathan/projects/coveralls-netcore/test/MyLib.Tests/obj/MyLib.Tests.csproj.nuget.g.targets.
  Generating MSBuild file /home/nathan/projects/coveralls-netcore/src/MyLib/obj/MyLib.csproj.nuget.g.targets.
  Restore completed in 655.78 ms for /home/nathan/projects/coveralls-netcore/src/MyLib/MyLib.csproj.
  Restore completed in 655.63 ms for /home/nathan/projects/coveralls-netcore/test/MyLib.Tests/MyLib.Tests.csproj.
Finished executing task: Restore

========================================
Build
========================================
Executing task: Build
Microsoft (R) Build Engine version 15.7.179.62826 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

/home/nathan/projects/coveralls-netcore/src/MyLib/MyLib.csproj : warning NU1603: MyLib depends on NETStandard.Library (>= 2.0.2-servicing-26420-0) but NETStandard.Library 2.0.2-servicing-26420-0 was not found. An approximate best match of NETStandard.Library 2.0.2 was resolved.
  MyLib -> /home/nathan/projects/coveralls-netcore/src/MyLib/bin/Release/netstandard2.0/MyLib.dll
  MyLib.Tests -> /home/nathan/projects/coveralls-netcore/test/MyLib.Tests/bin/Release/netcoreapp2.1/MyLib.Tests.dll

Build succeeded.

/home/nathan/projects/coveralls-netcore/src/MyLib/MyLib.csproj : warning NU1603: MyLib depends on NETStandard.Library (>= 2.0.2-servicing-26420-0) but NETStandard.Library 2.0.2-servicing-26420-0 was not found. An approximate best match of NETStandard.Library 2.0.2 was resolved.
    1 Warning(s)
    0 Error(s)

Time Elapsed 00:00:02.64
Finished executing task: Build

========================================
Test
========================================
Executing task: Test
Assembly resolver search directories:
/home/nathan/projects/coveralls-netcore/test/MyLib.Tests/obj/Release/netcoreapp2.1

Assembly resolver search directories:
/home/nathan/projects/coveralls-netcore/test/MyLib.Tests/bin/Release/netcoreapp2.1
/home/nathan/.nuget/packages
/opt/dotnet/sdk/NuGetFallbackFolder

Instrumenting assembly "MyLib"
Changing working directory to '/home/nathan/projects/coveralls-netcore'
Reset coverage for directory: '/home/nathan/projects/coveralls-netcore' on pattern './coverage-hits.txt'
Directory is already cleared
Test run for /home/nathan/projects/coveralls-netcore/test/MyLib.Tests/bin/Release/netcoreapp2.1/MyLib.Tests.dll(.NETCoreApp,Version=v2.1)
Microsoft (R) Test Execution Command Line Tool Version 15.3.0-preview-20170628-02
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
[xUnit.net 00:00:00.4964089]   Discovering: MyLib.Tests
[xUnit.net 00:00:00.5609486]   Discovered:  MyLib.Tests
[xUnit.net 00:00:00.5680125]   Starting:    MyLib.Tests
[xUnit.net 00:00:00.7413388]   Finished:    MyLib.Tests

Total tests: 1\. Passed: 1\. Failed: 0\. Skipped: 0.
Test Run Successful.
Test execution time: 1.6506 Seconds
Changing working directory to '/home/nathan/projects/coveralls-netcore'
+-------------------+-------+---------------+------------+
| File              | Lines | Covered Lines | Percentage |
+-------------------+-------+---------------+------------+
| src/MyLib/Math.cs |    2  |        1      |   50.000%  |
+-------------------+-------+---------------+------------+
| All files         |    2  |        1      |   50.000%  |
+-------------------+-------+---------------+------------+
Finished executing task: Test

Task                          Duration            
--------------------------------------------------
Restore                       00:00:01.9525117    
Build                         00:00:02.9521372    
Test                          00:00:06.5041439    
--------------------------------------------------
Total:                        00:00:11.4087928 
```

## 自动化测试&发布覆盖范围

现在我们有了一个构建脚本来运行我们的测试并生成覆盖率结果，我们可以将我们的 repo 连接到 [TravisCI](https://docs.travis-ci.com/user/getting-started) 和[工作服](https://docs.coveralls.io/)。按照入门指南将这两项服务连接到您的 GitHub 帐户并添加您的回购。然后，在 repo 的根目录下创建一个`.travis.yml`文件，内容如下:

```
language: csharp
mono: none
dotnet: 2.1

script:
- ./build/travis.sh 
```

我喜欢把我的 Travis 构建包在一个单独的脚本中。这样，如果我发布了一个 Docker 容器，我可以登录 Docker Hub 并只在主分支上推送映像，我可以用 Travis 公开的环境变量来检测它:

```
# ./build/travis.sh

#!/bin/bash
set -euo pipefail

SCRIPT_ROOT="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"
CAKE_TASK=Coveralls

echo "Building task ${CAKE_TASK}"
"${SCRIPT_ROOT}/../build.sh" -t "${CAKE_TASK}" 
```

我们还将添加一个新的蛋糕任务，将覆盖率结果上传到工作服:

```
Task("Coveralls")
    .IsDependentOn("Test")
    .Does(() => 
{
    if (!TravisCI.IsRunningOnTravisCI)
    {
        Warning("Not running on travis, cannot publish coverage");
        return;
    }

    MiniCoverReport(new MiniCoverSettings()
        .WithCoverallsSettings(c => c.UseTravisDefaults())
        .GenerateReport(ReportType.COVERALLS)
    );
}); 
```

连体工作服与 Travis 进行了很好的集成，我们只需要提供构建 ID，它就会自动计算出其余的提交信息。如果您不是在 TravisCI 中构建，您将需要指定附加信息。关于如何通过`Cake.MiniCover`提供该信息的细节，参见 [`CoverallsSettings`](http://cakebuild.net/api/Cake.MiniCover.Settings/CoverallsSettings/) 扩展方法。

现在，我们有 TravisCI 自动[构建&测试](https://travis-ci.org/nlowe/coveralls-netcore/builds/406664517)我们的代码，和套装[跟踪代码覆盖率](https://coveralls.io/builds/18100397)！

## 结论

我希望这能对你有所帮助。我希望能够在不需要工具项目的情况下提供 MiniCover 支持，但这需要将 MiniCover 分成一个核心库和一个 cli 工具:

# [![GitHub logo](../Images/04febf72106d337f3956ebfaf9121dae.png) 从其他工具中提取常用库来启用消费 #31](https://github.com/lucaslorentz/minicover/issues/31) 

[![nlowe avatar](../Images/4b238059065a2c0656bfbf9be773d784.png) ](https://github.com/nlowe)  **[nlowe](https://github.com/nlowe)** posted on [<time datetime="2018-02-26T05:46:14Z">Feb 26, 2018</time>](https://github.com/lucaslorentz/minicover/issues/31)

我想为 MiniCover 写一个类似于为 [OpenCover](https://cakebuild.net/api/Cake.Common.Tools.OpenCover/OpenCoverAliases/191C133A) 存在的[蛋糕](https://cakebuild.net)插件。我不能轻易指示用户添加 MiniCover 作为工具，因为 cake 从 Nuget 下载工具的方式与`DotnetCliTool`包类型不兼容。我可以告诉用户按照自述文件中的说明创建一个工具项目，并将其传递给我的插件，但我认为更简单的解决方案是将实际的工具和报告逻辑与 dotnet cli 工具前端分开。这将允许我在我的包中引用它作为一个依赖项，并使用户能够获得简单的覆盖，例如:

```
#addin "Cake.MiniCover"

// ...

Task("Coverage")
    .IsDependentOn("build")
    .Does(() => 
{
    MiniCover(tool =>
        {
            foreach(var project in GetFiles("./test/**/*.csproj"))
            {
                tool.DotNetCoreTest(project.FullPath, new DotNetCoreTestSettings()
                {
                    // Required to keep instrumentation added by MiniCover
                    NoBuild = true,
                    Configuration = configuration
                });
            }
        },
        new MiniCoverSettings()
            .WithAssembliesMatching("./test/**/*.dll")
            .WithSourcesMatching("./src/**/*.cs")
            .GenerateReport(ReportType.CONSOLE | ReportType.XML)
    );
});

// ...
```

然后，生成覆盖率就变得像

```
./build.sh -t Coverage

# Or on Windows:

./build.ps1 -t Coverage
```

[View on GitHub](https://github.com/lucaslorentz/minicover/issues/31)

如果你发现`Cake.MiniCover`有任何问题，一定要开一个[问题](https://github.com/nlowe/cake.minicover)！如果你有一个你想添加的特性，拉请求也是受欢迎的。