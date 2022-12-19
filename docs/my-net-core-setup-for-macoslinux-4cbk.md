# 我的。MacOS/Linux 的 NET Core 设置

> 原文：<https://dev.to/chrisvasqm/my-net-core-setup-for-macoslinux-4cbk>

很疯狂，对吧？

谁能想到微软会做出。NET 开发在 Windows 之外其实很棒！

我需要使用这些工具，因为我目前在大学学习的一门课程要求我用实体框架和一些更花哨的缩写词来做一个 ASP.NET MVC web 应用程序。

所以，今天我将回顾我开始做事的步骤。

在我们开始左右打印之前，我们必须安装一些工具:

# 工具

*   [。网芯 SDK](https://www.microsoft.com/net/download) 。
*   [码头工人](https://www.docker.com/)。
*   [VS 代码](https://code.visualstudio.com/)(免费)/ [骑手](https://www.jetbrains.com/rider/download/#section=mac)(部分学生付费或免费)。
*   [Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/download?view=sql-server-2017) (这个类似 SQL Management Studio)。

为了验证一切顺利，继续在您的终端中执行以下命令:

```
dotnet --version
2.1.403 
```

Enter fullscreen mode Exit fullscreen mode

和

```
docker --version
Docker version 18.06.1-ce, build e68fc7a 
```

Enter fullscreen mode Exit fullscreen mode

# 记录你的生活

由于我们目前没有适用于 MacOS 的微软 SQL Server 引擎，我不得不使用 Docker 在一个容器中安装 Linux 的 MS SQL Server，虽然这一开始听起来可能很复杂，但有一篇由 [@reverentgeek](https://dev.to/reverentgeek) 写的非常棒的帖子可以教你具体如何做，甚至更多！

[![reverentgeek](../Images/5ed0d8480a585c035ee1cacfb61fb18f.png)](/reverentgeek) [## 运行在 Mac 上的 SQL Server？！

### 大卫尼尔 10 月 3 日 175 分钟阅读

#docker #devops #sqlserver #node](/reverentgeek/sql-server-running-on-a-mac-29e)

# 编辑或不编辑

在我提到的*工具*部分，你可以使用 [VS 代码](https://code.visualstudio.com/)或者[骑士](https://www.jetbrains.com/rider/download/#section=mac)，所以你可以随意使用你喜欢的。

如果你使用 VS 代码，你将不得不安装 [C#语言扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)，你就可以开始了。

我个人更喜欢使用 Rider，因为我对基于 IntelliJ 的 ide 在功能和键盘快捷键方面比较熟悉。但是为了这篇文章，我将介绍使用 VS 代码的步骤，因为这是。NET 核心网站。

# Hello World-ing

为了启动一个项目。NET Core SDK 为您提供了一些可以在终端中使用的命令。

要创建一个项目，使用`cd <folder-name>`命令导航到您喜欢的文件夹，比如:

```
cd Documents 
```

Enter fullscreen mode Exit fullscreen mode

然后使用`mkdir <folder-name>`命令:
创建一个文件夹

```
mkdir CoreIsAwesome 
```

Enter fullscreen mode Exit fullscreen mode

然后使用
导航到它

```
cd CoreIsAwesome 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:这里有一个重要的细节，如果你使用下一个命令来创建一个. NET 核心项目，它将会像你当前所在的文件夹一样被命名，在这里是“CoreIsAwesome”。

现在我们可以使用下面的代码生成一个简单的控制台应用程序:

```
dotnet new console 
```

Enter fullscreen mode Exit fullscreen mode

您应该会很快看到类似这样的内容出现在您的终端中:

```
The template "Console Application" was created successfully.

Processing post-creation actions...
Running 'dotnet restore' on /Users/chrisvasqm/VisualStudioProjects/CoreIsAwesome/CoreIsAwesome.csproj...
  Restoring packages for /Users/chrisvasqm/VisualStudioProjects/CoreIsAwesome/CoreIsAwesome.csproj...
  Generating MSBuild file /Users/chrisvasqm/VisualStudioProjects/CoreIsAwesome/obj/CoreIsAwesome.csproj.nuget.g.props.
  Generating MSBuild file /Users/chrisvasqm/VisualStudioProjects/CoreIsAwesome/obj/CoreIsAwesome.csproj.nuget.g.targets.
  Restore completed in 211.77 ms for /Users/chrisvasqm/VisualStudioProjects/CoreIsAwesome/CoreIsAwesome.csproj.

Restore succeeded. 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该能够在资源管理器面板中看到您的项目的所有文件。

如果你选择一个文件，比如说`Program.cs`，你应该有下面的代码:

```
using System;

namespace CoreIsAwesome
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

再过几秒钟左右，VS 代码应该会显示这样一个对话框:

[![Required assets dialog](../Images/e79f4f37c4c324d1ec5ecdaed2e284dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WxCD05V0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tq48oq68htt043j9m3fb.png)

继续并点击“是”。

现在，如果你转到`Debug`面板，你的`Run Configuration`应该是这样的:

[![Run configuration](../Images/034358aafd659189e8c4cf3f833963ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fWWqFizC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3e0guy47oqj6mifo4yz5.png)

这意味着我们可以继续运行我们的代码！

交叉手指时按下绿色的`Play`按钮，这样你就不会出现任何错误。

等待几秒钟，直到构建完成...

再多一点点...

和...

瞧啊。

[![Hello World printed on the console](../Images/e81725f7dc80fc61d35a3683ae8598f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6nRd8cz3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8x92o9vnqp7ehoyd9g4j.png)

如果你放大或者用力挤压你的眼睛，你应该能看到那个小小的“你好世界！”在调试控制台中。

但是...

[![Remember, with great power comes great responsibility](../Images/89b312289c29d9ae6d7e7cd28a569440.png)T2】](https://i.giphy.com/media/MCZ39lz83o5lC/giphy.gif)

在我的例子中，唯一的不同是没有做:

```
dotnet new console 
```

Enter fullscreen mode Exit fullscreen mode

我用过:

```
dotnet new mvc 
```

Enter fullscreen mode Exit fullscreen mode

为我生成一个. NET 核心 MVC 模板，然后我必须在我的项目中设置实体框架核心依赖关系等等，但这可能是另一篇文章的主题；)

> *提示:如果您想知道`dotnet new`命令还能为您生成什么样的模板，请使用下面的命令:*

```
dotnet new --help 
```

Enter fullscreen mode Exit fullscreen mode

这应该给你所有你需要的指示。