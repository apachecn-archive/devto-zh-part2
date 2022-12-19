# 一大堆。网络核心 CI/CD (Lite)

> 原文：<https://dev.to/jeikabu/a-potpourri-of-net-core-cicd-lite-24ba>

我已经专业地使用 Visual Studio 大约十年了。 [Jenkins](https://jenkins.io/) 过去 5 年的 CI/CD。虽然两者的最新版本都是优秀的工具(特别是 [Jenkins pipeline](https://jenkins.io/solutions/pipeline/) ，但是我的开发环境感觉停滞不前。

我在家里使用 OSX，随着我们迁移到 Github，现在似乎是尝试不同事物的最佳时机。以下是我最近在 github 项目 [nng 中所做的一些脑力劳动。NETCore](https://github.com/subor/nng.NETCore) -。为 [nng](https://github.com/nanomsg/nng) 的网绑定。

## 点网 CLI

我之前在 OSX 用过 [MonoDevelop](https://www.monodevelop.com/) 。微软收购后，它被更名为[Mac 版 Visual Studio](https://docs.microsoft.com/en-us/visualstudio/mac/)。虽然比苹果的 Xcode 更舒服，但总感觉…笨重。[进入 dotnet CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x) :

```
mkdir l0core
cd l0core
# Create l0core.sln
dotnet new sln
# Create l0core/l0core.csproj
dotnet new console --name l0core
# Add l0core.csproj to l0core.sln
dotnet sln add l0core
# Add a bunch of existing projects

dotnet add l0core package <package_name> 
```

Enter fullscreen mode Exit fullscreen mode

让我吃惊的一件事是，尽管你可以用
将一个现有的项目添加到 sln 中

```
dotnet sln add ../layer0/Layer0Shared/ 
```

Enter fullscreen mode Exit fullscreen mode

不能轻易添加项目引用，例如:

```
# NB: This doesn't work
# In l0core.csproj add reference to Layer0Shared.csproj
dotnet add l0core reference Layer0Shared 
```

Enter fullscreen mode Exit fullscreen mode

而是要参考实际项目:

```
dotnet add l0core reference ../layer0/Layer0Shared/ 
```

Enter fullscreen mode Exit fullscreen mode

在 Visual Studio 中，我习惯于向解决方案中添加一个项目，然后添加对该项目的引用。

## Visual Studio 代码

一年多来，Visual Studio Code 一直是我的首选文本编辑器。特别是对于[的剪辑降价](https://rendered-obsolete.github.io/2018/02/16/markdown.html)。

创建项目后， [VS 代码 C#指南](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio-code)提到了调试和没用的东西。底部的[常见问题](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio-code#faq)提到了`.Net: Generate Assets for Build and Debug`。失败原因:

```
Could not locate .NET Core project. Assets were not generated. 
```

Enter fullscreen mode Exit fullscreen mode

好像是[这个 bug](https://github.com/OmniSharp/omnisharp-vscode/issues/1425) 。无论如何，关闭并重启 VS 代码似乎可以解决这个问题。

**开始调试**并且马上失败了很棒:

[![](../Images/55442b10df8f2a5bb6b74be5216ddf29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z1Dvb9Ky--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/vscode_debug.png)

从 1.26 开始(现在是 1.27.1)，在用它作为我的主要 C# IDE 几周后，我的抱怨是:

*   智能感知频繁停止工作(需要 **Cmd+Shift+p > `restart omnisharp`** )
*   没有[【任务】窗口](https://docs.microsoft.com/en-us/visualstudio/debugger/using-the-tasks-window?view=vs-2017)
*   启动调试器或运行测试被卡住。结束`killall dotnet`并重启 VS 代码
*   没有 [XML 文档](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/xmldoc/xml-documentation-comments)协助([这个扩展](https://marketplace.visualstudio.com/items?itemName=k--kato.docomment)就是全部)。在常规 Visual Studio 中:
    *   `///`开始注释块，按回车键缩进并插入`///`
    *   [XmlDoc 标签如`<see>`](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/xmldoc/recommended-tags-for-documentation-comments) 自动完成并使用智能感知
*   非平凡但常见的类型(例如`System.Assembly`)需要调试器一段时间来评估
*   缺少一些源代码分析/林挺；不需要的`using`语句、名称空间范围等。

为 [nng 的 Windows 和 Linux 版本设置](https://github.com/subor/nng.NETCore) [Appveyor](https://www.appveyor.com/) 。NETCore 。

通过 web 界面添加项目后，提交一个空的`appveyor.yml`或者点击 **NEW BUILD** 将触发一个构建和测试。

失败原因:

```
msbuild "C:\projects\nng-netcore\nng.NETCore.sln" /verbosity:minimal /logger:"C:\Program Files\AppVeyor\BuildAgent\Appveyor.MSBuildLogger.dll"
Microsoft (R) Build Engine version 14.0.25420.1
Copyright (C) Microsoft Corporation. All rights reserved.
C:\projects\nng-netcore\nng.NETCore\nng.NETCore.csproj(1,1): error MSB4041: The default XML namespace of the project must be the MSBuild XML namespace. If the project is authored in the MSBuild 2003 format, please add  to the <Project> element. If the project has been authored in the old 1.0 or 1.2 format, please convert it to MSBuild 2003 format. 
```

Enter fullscreen mode Exit fullscreen mode

Appveyor 默认使用不支持最新项目格式的 msbuild 14.0。将`appveyor.yml`更改为:

```
image: Visual Studio 2017 
```

Enter fullscreen mode Exit fullscreen mode

`git push`(或**新造** ):

```
C:\Program Files\dotnet\sdk\2.1.401\Sdks\Microsoft.NET.Sdk\targets\Microsoft.PackageDependencyResolution.targets(198,5): error NETSDK1004: Assets file 'C:\projects\nng-netcore\nng.NETCore\obj\project.assets.json' not found. Run a NuGet package restore to generate this file. [C:\projects\nng-netcore\nng.NETCore\nng.NETCore.csproj]
Done Building Project "C:\projects\nng-netcore\nng.NETCore\nng.NETCore.csproj" (default targets) -- FAILED. 
```

Enter fullscreen mode Exit fullscreen mode

需要恢复的包:

```
before_build:
  - dotnet restore 
```

Enter fullscreen mode Exit fullscreen mode

减少构建垃圾日志的数量:

```
build:
  verbosity: minimal 
```

Enter fullscreen mode Exit fullscreen mode

## 用 xUnit 测试

我们当前的项目使用的是 NUnit，所以我对此很满意。 [xUnit 似乎是唯一推荐的不是 MSTest 的测试框架](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-test?tabs=netcore21#filter-option-details)。

我喜欢使用参数化测试来运行变体并覆盖多个代码路径。一个[少数](https://andrewlock.net/creating-parameterised-tests-in-xunit-with-inlinedata-classdata-and-memberdata/) [的博客](http://hamidmosalla.com/2017/02/25/xunit-theory-working-with-inlinedata-memberdata-classdata/) [的帖子](http://ikeptwalking.com/writing-data-driven-tests-using-xunit/)封面怎么做:

```
class TransportsClassData : IEnumerable<object[]>
{
    public IEnumerator<object[]> GetEnumerator()
    {
        yield return new object[] { "ws://localhost:23123" };
        yield return new object[] { "tcp://localhost:23124" };
    }

    IEnumerator IEnumerable.GetEnumerator() => GetEnumerator();
}

[Theory]
[ClassData(typeof(TransportsClassData))]
public async Task Basic(string url)
{
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

需要相当于 Nunit 的 SetupFixtureAttribute 的[来执行一次性设置，如](https://github.com/nunit/docs/wiki/SetUpFixture-Attribute)[加载 nng 本机/非托管库](///2018/09/09/native-assembly.html) :

```
public class NngCollectionFixture
{
    public NngCollectionFixture()
    {
        // ...
    }
}

[CollectionDefinition("nng")]
public class NngCollection : ICollectionFixture<NngCollectionFixture>
{
}

[Collection("nng")]
public class BusTests
{
    public BusTests(NngCollectionFixture collectionFixture)
    {
        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

Appveyor 自动选择所有的测试，不需要任何额外的配置。更多信息见[运行测试](https://www.appveyor.com/docs/running-tests/)。

## 代码覆盖率

使用 [codecov](https://codecov.io/) 进行代码覆盖。以下参考资料涵盖了所有内容:

*   [https://www.appveyor.com/blog/2017/03/17/codecov/](https://www.appveyor.com/blog/2017/03/17/codecov/)
*   [https://github.com/codecov/example-csharp](https://github.com/codecov/example-csharp)
*   [https://github.com/OpenCover/opencover/wiki/Usage](https://github.com/OpenCover/opencover/wiki/Usage)

在所有你想要启用代码覆盖率的项目中添加:

```
<PropertyGroup>
  <DebugType>full</DebugType>
</PropertyGroup> 
```

Enter fullscreen mode Exit fullscreen mode

在`appveyor.yml` :

```
before_build:
  - dotnet restore
  - choco install opencover.portable
  - choco install codecov

test_script:
  - OpenCover.Console.exe -register:user -target:dotnet.exe -targetargs:test -output:".\coverage.xml" -oldstyle

after_test:
  - codecov -f "coverage.xml" -t 2a07cd3d-8620-4495-8c14-3252b10b90bd 
```

Enter fullscreen mode Exit fullscreen mode

1.  使用[巧克力](https://chocolatey.org/)安装[开盖](https://github.com/OpenCover/opencover)
2.  打开盖子运行`dotnet test`
3.  将结果上传到 codecov

## Linux

用一个 [Linux 版本](https://www.appveyor.com/docs/getting-started-with-appveyor-for-linux/#running-windows-and-linux-builds-side-by-side)和两个调试/发布配置来完善 CI:

```
image: 
- Visual Studio 2017
- Ubuntu
configuration:
  - Debug
  - Release 
```

Enter fullscreen mode Exit fullscreen mode

对于仅适用于 Windows 的命令和仅适用于 Linux 的命令，可以分别以`cmd:`或`sh:`为前缀。两者都没有。巧克力味的东西会大大降低构建速度(增加几分钟)，所以只在 Windows 上运行代码覆盖率:

```
before_build:
  - dotnet restore
  - cmd: choco install opencover.portable
  - cmd: choco install codecov

test_script:
  - cmd: OpenCover.Console.exe -register:user -target:dotnet.exe -targetargs:"test --filter platform!=posix" -output:".\coverage.xml" -oldstyle
  - sh: dotnet test --filter "platform!=windows" 
```

Enter fullscreen mode Exit fullscreen mode

使用`--filter platform!=XYZ`避免特定于平台的测试。在测试代码:

```
[Fact]
[Trait("platform", "windows")]
public void WindowsOnlyTest()
{
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

参见[过滤器选项详情](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-test?tabs=netcore21#filter-option-details)和[运行选择性单元测试](https://docs.microsoft.com/en-us/dotnet/core/testing/selective-unit-tests)了解更多选项。

## 部署

[分支](https://www.appveyor.com/docs/branches/)展示了如何在不创建多个`appveyor.yml`文件的情况下进行依赖于分支的构建配置。[部署](https://www.appveyor.com/docs/deployment/)涵盖一般部署主题。

当有 git 标记时，希望将部署限制到特定的分支。[将工件推送到 Github 版本](https://www.appveyor.com/docs/deployment/github/#configuring-in-appveyoryml)显示了 appveyor 的配置，我随后[将其适配到 Nuget](https://www.appveyor.com/docs/deployment/nuget/) :

```
deploy:
  provider: NuGet
  api_key:
    secure: OSKjxq8SQmVX8UaVkgaq1aUeGnuXHiTzNZoIi2VR0OMCp/WypCkBY7JbkmoKz497
  artifact: /.*\.nupkg/
  on:
    branch: master
    appveyor_repo_tag: true 
```

Enter fullscreen mode Exit fullscreen mode

`api_key`值来自[加密](https://ci.appveyor.com/tools/encrypt)我的 [nuget 上传令牌](//./signing-a-nupkg-and-uploading-to-nugetorg-2a98)。

显而易见的问题是，没有我的私钥，我就不能。现在可能不得不满足于部署到 Github 版本。

## 天赋

没有天赋的 github 项目是不完整的。

[![NuGet](../Images/f7115f0ffee8a104ad840dd8adb05c95.png)](https://www.nuget.org/packages/Subor.nng.NETCore) 途经:

```
[![NuGet](https://img.shields.io/nuget/v/PACKAGE.svg?colorB=COLOR)](https://www.nuget.org/packages/PACKAGE) 
```

Enter fullscreen mode Exit fullscreen mode

[![Build status](../Images/d68bb9ac749c52b9ebc5b14f0c2c79cf.png)](https://ci.appveyor.com/project/jake-ruyi/nng-netcore/branch/master) 出自 [shields.io 例句](https://shields.io/#/examples/build) :

```
[![Build status](https://img.shields.io/appveyor/tests/USERNAME/PROJECT/BRANCH.svg)](https://ci.appveyor.com/project/USERNAME/PROJECT/branch/BRANCH) 
```

Enter fullscreen mode Exit fullscreen mode

[![codecov](../Images/43e420d897b263b85eba8ac5a5978566.png) ](https://codecov.io/gh/subor/nng.NETCore) from Codecov.io 回购**设置>徽章** :

```
[![codecov](https://codecov.io/gh/GITHUB_OWNER/PROJECT/branch/BRANCH/graph/badge.svg)](https://codecov.io/gh/GITHUB_OWNER/PROJECT) 
```

Enter fullscreen mode Exit fullscreen mode

## 码头工人

在 OSX 上工作时，如果能够在 Windows 和 Linux 上进行构建/测试，而不必等待 Appveyor，那会很方便。[输入 Docker](https://docs.microsoft.com/en-us/dotnet/core/docker/docker-basics-dotnet-core) 。

创建`Dockerfile` :

```
FROM microsoft/dotnet:2.1-sdk
WORKDIR /app

COPY . ./
RUN dotnet restore
RUN dotnet test tests --filter platform!=windows 
```

Enter fullscreen mode Exit fullscreen mode

运行:

```
docker build -t dotnetapp-dev . 
```

Enter fullscreen mode Exit fullscreen mode

过去曾尝试过 Docker。虽然它确实很酷，但从未真正“全身心投入”并使其成为我们开发管道的一部分。现在 Windows 作为一等公民得到了支持，我将对它进行另一种更严肃的审视。