# 穷人的网络解决方案设置

> 原文：<https://dev.to/rafalpienkowski/poor-mans-dotnet-solution-setup-1g7l>

不久前，我开始了使用 dotnet Core 的 Linux 之旅。

最初，我计划描述如何建立一个工作解决方案，但是我在 dev.to .上找到了一篇关于这个的文章。瑞几乎涵盖了我打算描述的所有重要的事情。如果你不知道如何在 Linux 中开始 ASP.NET 核心开发，我鼓励你阅读他的文章。干得好，芮！

因为在那个主题上我没有什么可补充的，所以我决定专注于另一个主题。在我之前的文章中，有些人建议我使用 [Jetbrain 的 Rider IDE](https://www.jetbrains.com/rider/) 。也许在未来，我会买它，尤其是如果我决定从 Windows 转到 Linux/iOS。我认为在这种情况下，花费 350 美元购买一个 IDE 是合理的。现在，对于我的副业项目，我将专注于 VS 代码。

让我抓狂的是一个项目初始化。我厌倦了不断输入相同的命令行命令来建立一个新的解决方案。和每一个开发者一样，我是一个懒惰的人。

[![Lazy](img/ac9105d3dad2680d38bd46030c3753bf.png)T2】](https://i.giphy.com/media/VjWNQMfCIHxS0/giphy.gif)

为了避免一遍又一遍地做这些无聊的事情，我决定写一个 bash 脚本来代替我做所有那些乏味的工作。它的源代码可以在我的 GitHub repo 上找到。

该脚本创建一个基本解决方案的文件夹结构。它初始化主项目和专用测试项目。创建的测试项目包含对主项目的引用。它还参考了两个有利的(至少在我看来)NuGet 包:[fluent assessments](https://fluentassertions.com)和 [Moq](https://github.com/Moq/moq4/wiki/Quickstart) 。它创建 sln 文件，这对于计划通过完整的 Visual Studio 打开解决方案的开发人员来说可能是有益的。最终会打开 Visual Studio 代码。

用法:

```
dotnet-solution [solution-name] [main-project-name] <<template>> 
```

Enter fullscreen mode Exit fullscreen mode

模板被传递给[dotnet new]命令。它将用于创建基于它的新 dotnet 项目。更多关于模板的信息可以在[链接](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-new)下找到。

示例:

```
dotnet-solution adventure-works AdventureWorks.Common classlib 
```

Enter fullscreen mode Exit fullscreen mode

将由上述示例创建的解决方案结构。我有意省略了一些无意义的文件夹。

```
+-- adventure-works
|   +-- src
|       +-- AdventureWorks.Common
|       |   +-- Class1.cs
|       |   +-- AdventureWorks.Common.csproj
|   +-- test
|       +-- AdventureWorks.Common.Tests
|       |   +-- UnitTest1.cs
|       |   +-- AdventureWorks.Common.Tests.csproj
+-- adventure-works.sln 
```

Enter fullscreen mode Exit fullscreen mode

对于那些不熟悉 Linux/Unix 系统的人来说，要向 bash shell 添加特定的扩展，您需要:

*   下载到你的机器上
*   复制到/usr/bin 目录(需要管理员权限)
*   通过 *chmod* 命令使其可执行(需要管理员权限)

```
 chmod +x dotnet-solution 
```

Enter fullscreen mode Exit fullscreen mode

请随意复制、使用和修改这些脚本。我也欢迎你所有的建议和主张。请记住，这是我学习 bash 脚本的第一步。希望能对你有所帮助。