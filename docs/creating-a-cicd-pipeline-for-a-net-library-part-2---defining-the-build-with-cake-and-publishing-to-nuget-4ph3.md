# 为. NET 库创建 CI/CD 管道:第 2 部分——用 Cake 定义构建并发布到 NuGet

> 原文：<https://dev.to/joaofbantunes/creating-a-cicd-pipeline-for-a-net-library-part-2---defining-the-build-with-cake-and-publishing-to-nuget-4ph3>

# [T1】简介](#intro)

在这篇文章中，我将讨论使用 [Cake](https://cakebuild.net/) 定义构建步骤，并将结果发布到 [NuGet](https://www.nuget.org) 。来自官方网站:“Cake (C# Make)是一个跨平台的构建自动化系统，带有 C# DSL，用于编译代码、复制文件和文件夹、运行单元测试、压缩文件和构建 NuGet 包等任务。”

# 为什么要蛋糕

Cake 是定义构建一个项目需要做什么的几个选项之一。

要定义 CI/CD 任务，首先想到的可能是使用 AppVeyor、VSTS、Travis CI 和所有其他选项已经提供的工具。

这种方法的第一个问题是在本文介绍的案例中，我们希望构建多个 CI 提供者来测试多个操作系统，这意味着在每个提供者中重复这些任务(如果我们希望迁移到不同的提供者，情况也是如此)。

使用 Cake 而不是直接依赖 CI 提供者的另一个优点是，我们可以在本地运行构建脚本。这不仅对测试有用，而且如果我们的构建不是非常简单，我们可以在开发时使用脚本。

另一种选择是创建 PowerShell 或 Shell 脚本来定义任务。这将是与提供商无关的，但可能不是与操作系统无关的(尽管 PowerShell 现在也可以在其他平台上运行，所以这是可能的)。

Cake 解决了这些问题，并增加了一些额外的好处:

*   我们用 C#写东西，这对许多 C#开发者来说可能更容易
*   有许多内置的和可插拔的助手
*   此外，最坏的情况是缺少一些东西，这是 C#，我们可以只编码缺少的东西！(我在这篇文章中有一个例子)

# 自举蛋糕

在我们开始定义构建之前，我们需要启动 Cake。这可以通过从资源库[的](https://github.com/cake-build/resources)中下载`build.ps1`和`build.sh`来完成。然后执行这些文件中的一个(取决于您的开发操作系统)将下载所需的依赖项并创建一个`build.cake`文件，在这里将定义构建。

# 定义构建任务

现在我们可以打开我们的`build.cake`文件，开始定义所需的任务。我将遍历我的项目的构建定义文件(在阅读了[这篇文章](https://dev.to/jandedobbeleer/dotnet-core-served-with-a-slice-of-cake-5972)之后，它被组织得更好了)。

在文件的开头，我声明了将要使用的插件和工具的依赖关系。还有一个`using NuGet;`语句，因为就像我之前提到的，这是 C#，我想做一些恶作剧。