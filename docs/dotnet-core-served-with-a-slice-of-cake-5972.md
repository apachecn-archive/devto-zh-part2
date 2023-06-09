# 点网核心配一片蛋糕

> 原文：<https://dev.to/jandedobbeleer/dotnet-core-served-with-a-slice-of-cake-5972>

在准备介绍 DevOps 的幻灯片时，我准备给一群渴望知识的人看。NET 开发人员，我希望建立一个示例项目来说明一个很好的设置开始。DotNet Core 是已知的，它有很好的 CLI 界面和在任何地方运行的能力，**选择是显而易见的**。但是当涉及到构建工具时，我觉得我所知道的那些工具(psake，fake)很容易让不熟悉整个*构建的人大吃一惊。Visual Studio 之外的 NET 代码*之类的东西。街区里有一个孩子已经在我的关注名单上有一段时间了，你知道**我有多喜欢转移话题**，花太多时间探索令人兴奋的新事物！

长话短说，我爱上了蛋糕。与其他构建自动化工具相比，它提供了许多不错的第一方和第三方集成，减少了对样板代码的需求。之前，我使用**高度定制的 PowerShell 脚本**来版本化和上传工件和测试结果，Cake 自带了所有这些。另一个优点是，如果您正在寻找的功能不可用，它很容易扩展。所有的赢家\0/。

那么，我的理想设置是什么样的呢？好吧，当谈到一个好的 CI 设置时，我们需要一些东西。首先，我希望所有的构建逻辑都从源代码中管理，不依赖于 GUI，因为它很容易成为维护和切换上下文的负担。从好的方面来说，我们希望能够在本地以及在我们的构建代理上运行它，Cake 将帮助我们实现这一目标。其次，这里有一个我正在寻找的东西的列表，蛋糕应该为我处理:

*   将构建逻辑拆分为任务
*   将任务分组以允许不同类型的构建
*   提供标准构建功能(清理、恢复、构建)
*   恢复 NuGet 包
*   启用静态代码分析和林挺
*   运行单元测试并上传覆盖率

让我们来看看其中的每一项。

## 拆分任务中的构建逻辑

要开始使用 Cake，您可以使用[引导程序](https://cakebuild.net/docs/tutorials/setting-up-a-new-project)。有两个版本，一个用于 Windows，一个用于 Unix 系统。我在我的示例项目中添加了这两个，只要确保在每个平台上使用正确的行尾，或者**到处使用 LF**(是的，我说过)。如果你使用的是 Visual Studio 代码，可以随意看看[蛋糕扩展](https://marketplace.visualstudio.com/items?itemName=cake-build.cake-vscode)，加里·伊万·帕克甚至制作了[附带视频](https://www.youtube.com/playlist?list=PL84yg23i9GBg7_7an5Qbo0Qllg-l2e-q-)来解释它的功能。

安装引导程序后，确保固定蛋糕版本。您可以通过在`tools/packages.config`添加一个包含以下内容的文件来实现。

```
<?xml version="1.0" encoding="utf-8"?>
<packages>
    <package id="Cake" version="0.27.2" />
</packages> 
```

Enter fullscreen mode Exit fullscreen mode

有了所有这些，除了项目文件之外，我们还有以下文件:

```
.
build.ps1
build.sh
tools
- packages.config 
```

Enter fullscreen mode Exit fullscreen mode

记住，我们已经建立了一个 DotNet 核心项目，如果你还没有，可以随意复制[样本项目](https://github.com/JanDeDobbeleer/dotnet-core-sample)来跟进。bootstrapper 文件`build.ps1`和`build.sh`的目的是能够**解析所有依赖关系**，包括 Cake，而不必担心这些。你可以看看源代码，看看他们做了什么，但是我们真正感兴趣的文件是`build.cake`。

创建`build.cake`文件，从现在开始它将包含我们所有的构建逻辑。蛋糕网站提供了一个很好的[入门](https://cakebuild.net/docs/tutorials/setting-up-a-new-project)样本，所以让我们从那里开始吧。

```
var target = Argument("target", "Default");

Task("Default")
  .Does(() =>
{
  Information("Hello World!");
});

RunTarget(target); 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到我们已经满足了第一个要求。我们有能力定义包含我们需要的任何逻辑的任务。完全用 C#编写，这是一个巨大的优势，**降低了作为. NET 开发者的启动成本**。名为`Default`的任务是在没有定义参数时运行的任务。通过在您选择的 CLI 工具中使用 bootstrapper，您可以立即看到它做了什么:`./build.ps1`(但说真的，使用 [ConEmu](https://conemu.github.io/) )。输出如下所示:

```
Preparing to run build script...
Running build script...

========================================
Default
========================================
Hello World!

Task                          Duration
--------------------------------------------------
Default                       00:00:00.0136945
--------------------------------------------------
Total:                        00:00:00.0136945 
```

Enter fullscreen mode Exit fullscreen mode

## 将任务分组以允许不同类型的构建

使用 Cake，很容易将几个任务组合成一个。假设我们想将 Hello world 任务分成两个有意义的任务，并将组合指定为`Default`。
我们可以定义单独的任务，让`Default`同时依赖于这两个任务。

```
var target = Argument("target", "Default");

Task("Hello")
  .Does(() =>
{
  Information("Hello");
});

Task("World")
  .Does(() =>
{
  Information("World");
});

Task("Default")
  .IsDependentOn("Hello")
  .IsDependentOn("World");

RunTarget(target); 
```

Enter fullscreen mode Exit fullscreen mode

当您使用`./build.ps1`运行时，您可以看到两个任务都在执行。既然我们已经了解了基础知识，是时候为我们的 DotNet 核心应用程序实现实际的逻辑了。

## 提供标准构建功能(清理、恢复、构建)

Cake 通过内置的第三方[插件](https://cakebuild.net/dsl/dotnetcore/)为 DotNet Core 提供支持。要清理一个项目，您可以使用`DotNetCoreClean`，但是在我的例子中，因为我习惯于自己清理，所以我在示例项目中使用了一个[自定义实现](https://github.com/JanDeDobbeleer/dotnet-core-sample/blob/master/build.cake#L40)。主要原因是来自 build 的输出要比来自 MSBuild 的输出多得多，我想确保一切都整洁有序。如果您选择内置的，这个任务看起来会像这样。

```
Task("Clean")
  .Does(() =>
{
  DotNetCoreClean("./src/project");
}); 
```

Enter fullscreen mode Exit fullscreen mode

请记住，您可以在这里做任何您想做的事情，所以也许您对此没有意见，或者更喜欢一种定制的方法，选择权在您。

除了能够定义一些设置之外，恢复 nuget 包和构建项目的方法几乎是相同的。你可以在蛋糕[网站](https://cakebuild.net/dsl/dotnetcore/)上找到所有信息，或者偷看一下[的样品项目](https://github.com/JanDeDobbeleer/dotnet-core-sample/blob/master/build.cake#L72)以获得灵感。

## 启用静态代码分析和林挺

现在我们已经准备好了基本的设置，我们终于可以开始有趣的部分了。当我们想到。NET 和代码分析，希望你忍不住去想 Resharper。巧合的是，JetBrains 提供了**两个 CLI 工具** ，inspectCode 和 dupFinder。第一个处理 Resharper 中包含的所有检查，第二个将寻找重复的代码块。**最酷的是**，我们可以让它验证我们通过 Visual Studio 执行的相同规则集，方法是将它们保存到项目的`DotSettings`文件中。你可以在这里阅读更多关于[的内容，我们将在这个示例中使用“解决方案团队共享”层。](https://www.jetbrains.com/help/resharper/Sharing_Configuration_Options.html)

同样值得一提的是，Visual Studio 中的 Resharper 提供了对[的支持。editorconfig](http://editorconfig.org/) 文件，但是这些文件不能与 CLI 版本结合使用。我花了大约一个小时的时间，在[马腾·巴利奥](https://twitter.com/maartenballiauw/status/988094504531095552)的帮助下，试图让它工作起来，最终得出这个结论。

为了在我们的 Cake 脚本中使用这些工具，我们需要一些依赖项。首先，也许这对你来说根本不是问题，我们需要 [Visual Studio 构建工具](https://chocolatey.org/packages/visualstudio2017buildtools/15.2.26430.20170605)并启用 DotNet 核心支持。作为一个只使用 Visual Studio 代码的开发人员，我没有预装这些，导致了很多痛苦和不清楚的错误。所以，如果你和我一样，在你的主机上安装这些，或者如果还没有安装的话，在你的机器上安装。其次，我们可以使用一个蛋糕插件，叫做 [Resharper](https://cakebuild.net/dsl/resharper/) ，你猜对了。要包含它，在您的`build.cake`脚本的顶部添加下面一行。这将使引导程序在运行 Cake 时解决依赖性。

```
#tool "nuget:https://www.nuget.org/api/v2?package=JetBrains.ReSharper.CommandLineTools&version=2018.1.0" 
```

Enter fullscreen mode Exit fullscreen mode

确保**锁定外部依赖项的版本号**，以避免不必要的副作用。这些工具的更新应该总是事先经过验证。

剩下的就是创建任务来验证我们的代码。

```
Task("DupFinder")
    .Description("Find duplicates in the code")
    .Does(() =>
{
    var settings = new DupFinderSettings() {
        ShowStats = true,
        ShowText = true,
        OutputFile = $"{artifactsDir}/dupfinder.xml",
        ExcludeCodeRegionsByNameSubstring = new string [] { "DupFinder Exclusion" },
        ThrowExceptionOnFindingDuplicates = true
    };
    DupFinder("./App.sln", settings);
});

Task("InspectCode")
    .Description("Inspect the code using Resharper's rule set")
    .Does(() =>
{
    var settings = new InspectCodeSettings() {
        SolutionWideAnalysis = true,
        OutputFile = $"{artifactsDir}/inspectcode.xml",
        ThrowExceptionOnFindingViolations = true
    };
    InspectCode("./App.sln", settings);
}); 
```

Enter fullscreen mode Exit fullscreen mode

它将使用我们现成的`DotSettings`文件，所以不需要指定它。您可以查看[示例](https://github.com/JanDeDobbeleer/dotnet-core-sample/blob/master/App.sln.DotSettings)来查看语法，如果您需要知道在哪里可以找到这些规则的名称，请查看[文档](https://www.jetbrains.com/help/resharper/Reference__Code_Inspections_CSHARP.html)。

我们可以添加另一个有趣的工具叫做 DependenciesAnalyser。它会查看您的 nuget 依赖项，看看它们是否过时。它不会使构建失败，但是把它作为**包含进来是一件好事，否则你可能会忘记**(我知道我会忘记)。当我在写的时候运行它，它提供了下面的输出，告诉我我可以更新两个依赖项。

编辑:看来这使得 UNIX 版本[失败](https://ci.appveyor.com/project/JanJoris/dotnet-core-sample/build/1.0.18/job/ijjicjy5joieu8jn)。我在这个项目上创造了一个[问题](https://github.com/joaoasrosa/dotnet-project-dependencies-analyser/issues/12)来提醒自己去看一看。目前，我从示例中删除了任务。

```
========================================
Analyse-Dependencies
========================================
Executing task: Analyse-Dependencies
Resolving assembly Domain.XmlSerializers, Version=2.0.0.0, Culture=neutral, PublicKeyToken=null
---------------------------------
Project: ./App\App.csproj
---------------------------------

---------------------------------
Project: ./Test\Test.csproj
coverlet.msbuild is on version 2.0.0\. The dependency is outdated.
FluentAssertions is on version 5.3.0\. The dependency is up-to-date.
Microsoft.NET.Test.Sdk is on version 15.7.2\. The dependency is outdated.
xunit is on version 2.3.1\. The dependency is up-to-date.
xunit.runner.visualstudio is on version 2.3.1\. The dependency is up-to-date.
---------------------------------

Finished executing task: Analyse-Dependencies 
```

Enter fullscreen mode Exit fullscreen mode

为了方便起见，我们可以将这些任务组合成一个任务，以便在需要时单独运行检查。

```
Task("Validate")
    .Description("Validate code quality using Resharper CLI. tools.")
    .IsDependentOn("Analyse-Dependencies")
    .IsDependentOn("DupFinder")
    .IsDependentOn("InspectCode"); 
```

Enter fullscreen mode Exit fullscreen mode

## 运行单元测试并上传覆盖率

在 DotNet 上编写单元测试一直是一种乐趣。我的基本设置由少数赢家(*意见*，毕竟是我的博客)组成，比如 [xUnit](https://xunit.github.io/) 和[流利断言](https://fluentassertions.com/)。使用`dotnet new xunit`命令可以创建一个测试项目，安装包遵循`dotnet add package FluentAssertions`语法。您可以使用`dotnet test`来运行测试。如果你需要更深入的介绍，可以在[微软网站](https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-dotnet-test)上找到示例设置和解释。

与其他标准的 DotNet 核心功能相比，Cake 提供了同样的易用性，这使得我们的任务看起来像这样。

```
Task("Test")
    .Description("Run all unit tests within the project.")
    .Does(() =>
{
    DotNetCoreTest("./Test/Test.csproj");
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，这给了我们运行单元测试的能力，但是如何计算代码覆盖率呢？原来有一个 MSBuild 扩展可以做到这一点。它被称为[床罩](https://github.com/tonerdo/coverlet)，您可以将它作为 nuget 包添加到您的测试项目:`dotnet add package coverlet.msbuild`。为了使用它的功能，我们需要向`Test`任务添加一些额外的参数。

```
var settings = new DotNetCoreTestSettings
     {
         ArgumentCustomization = args => args.Append("/p:CollectCoverage=true")
                                             .Append("/p:CoverletOutputFormat=opencover")
                                             .Append("/p:ThresholdType=line")
                                             .Append($"/p:Threshold={coverageThreshold}")
     };
DotNetCoreTest("./Test/Test.csproj", settings); 
```

Enter fullscreen mode Exit fullscreen mode

它所做的是将这些参数添加到 MSBuild 中，然后 MSBuild 将使用 coverlet 来计算覆盖率。我们想要线覆盖，我们可以使用`/p:ThresholdType=line`来指定。这将根据单元测试是否覆盖了每个相关的行来计算百分比。如果百分比低于某个阈值(定义为`build.cake`的 op top)，构建将失败。输出格式是 opencover，我们需要将结果导出到[coverals . io](https://coveralls.io/)，在这里我们可以跟踪我们的覆盖交叉构建和交叉分支。使用外部服务的优势在于，您不能简单地调整阈值来修复构建失败。例如，当在 GitHub 中以这种方式配置时，它还会阻止您在覆盖率下降的情况下将更改合并到 master 中(阈值可以对此很灵活)。

要将更改上传到 workalls . io，我们需要三个额外的依赖项。

```
#tool "nuget:https://www.nuget.org/api/v2?package=coveralls.io&version=1.4.2"
#addin "nuget:https://www.nuget.org/api/v2?package=Cake.Coveralls&version=0.8.0"
#addin "nuget:https://www.nuget.org/api/v2?package=Cake.Incubator&version=2.0.1" 
```

Enter fullscreen mode Exit fullscreen mode

[需要孵化器](https://cakebuild.net/api/Cake.Incubator/EnvironmentExtensions/31E6B762)来处理环境变量，因为**我们不想在构建脚本**中包含我们的 Coveralls.io API 令牌(说真的，不要)。无论使用何种 CI 工具，您都可以添加环境变量以在构建逻辑中使用。正确的方法是在构建脚本中将它们作为加密变量，这样它们就不能在 CI 阶段之外使用，但是您仍然可以通过源代码控制来调整它们。我自己正在使用 [Appveyor](https://www.appveyor.com/) ，它可以[加密变量](https://ci.appveyor.com/tools/encrypt)添加到你的`appveyor.yml`脚本中。

为了通过 Cake 上传覆盖率统计数据，我们使用了[工作服](https://cakebuild.net/addins/code-coverage/#cake-coveralls)附件。将它与孵化器结合起来，在构建期间检索 API 令牌，我们可以上传由 coverlet 生成的文件。

```
Task("Upload-Coverage")
    .Does(() =>
{
    var isRunningOnAppveyor = EnvironmentVariable<bool>("APPVEYOR", false);
    if (!isRunningOnAppveyor)
        return;
    Information("Running on Appveyor, uploading coverage information to coveralls.io");
    CoverallsIo("./Test/coverage.xml", new CoverallsIoSettings()
    {
        RepoToken = EnvironmentVariable("coveralls_token")
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经获得了所有这些，我们可以定义一个在构建机器上执行的基本 CI 任务。

```
Task("CI")
    .Description("Build the code, test and validate")
    .IsDependentOn("Build")
    .IsDependentOn("Test")
    .IsDependentOn("Validate")
    .IsDependentOn("Upload-Coverage"); 
```

Enter fullscreen mode Exit fullscreen mode

随着我们进入一个 C#能够在除 Windows 之外的其他机器上运行的世界，我也做了一个特定于 Unix 的构建任务。

```
Task("CI-UNIX")
    .Description("Build the code, test and validate")
    .IsDependentOn("Build")
    .IsDependentOn("Test"); 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我们让 Appveyor 在 Windows(包括静态分析和代码覆盖上传)和 Linux 上运行测试，让 [Travis.ci](https://travis-ci.org) 在 OSX 上运行测试。因为我们已经有了一个蛋糕构建脚本，所以我们只需要两个配置文件来告诉我们的 CI 实例做什么。注意，Appveyor 可以在 Windows 和 Linux 上运行 PowerShell，所以我们需要在执行相同的命令两次之前检查我们在哪里。

```
image:
- Visual Studio 2017
- Ubuntu
environment:
  coveralls_token:
    secure: HMJLoYbkSD1p35JnHiBjv02frHKMvasoD9J6wLnzjSUsdPwFwiHU8t0SwcvyAYi1
build_script:
  - pwsh: |
      if ($isWindows) {
        .\build.ps1 -Target CI
      }
  - sh: ./build.sh --target=CI-UNIX 
```

Enter fullscreen mode Exit fullscreen mode

```
language: csharp
mono: latest
dotnet: 2.1.200
os: osx
script:
  - ./build.sh --target=CI-UNIX 
```

Enter fullscreen mode Exit fullscreen mode

如果你对产量或建造时间感到好奇，你可以在 [Appveyor 的](https://ci.appveyor.com/project/JanJoris/dotnet-core-sample)或 [Travis 的](https://travis-ci.org/JanDeDobbeleer/dotnet-core-sample)网站上看看这个项目。

这是我最想做的。我会尽量让项目与有趣的附加组件保持同步，或者也许你知道一些改进，并想提出一个公关？不要犹豫，联系我们吧！

[源代码](https://github.com/JanDeDobbeleer/dotnet-core-sample)

最初发布于:[https://blog.itdepends.be/dotnet-core-cake/](https://blog.itdepends.be/dotnet-core-cake/)