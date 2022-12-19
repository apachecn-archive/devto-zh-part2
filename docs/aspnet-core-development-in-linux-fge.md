# Linux 中的 ASP.NET 核心开发

> 原文：<https://dev.to/ruidfigueiredo/aspnet-core-development-in-linux-fge>

什么时候。Net Core 问世后，人们对开发的想法非常兴奋。Windows 外部的. Net 代码。

的首选 IDE。Net，Visual Studio，现在是而且可能永远是一个 Windows 专用程序。

尽管有其他选择，今天在 Windows 之外完全开发 ASP.NET 核心应用程序是可能的。

在这篇博文中，我们将深入探讨这个问题，特别是在 Linux 中。

请注意，这里描述的所有工具都是跨平台的，所有这些工具都可以在任何支持。网芯。

[![.NET Core Linux](../Images/a0191ffab17e0ac5f31e3052c6cf2fca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bBAV4sEw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/03/NET-CoreLinux.png)

## IDE

Visual Studio 的完整版仅在 Windows 中可用。然而，有一个替代方案有一个有点令人困惑的名字: [Visual Studio 代码](https://code.visualstudio.com/)。

您可能认为 VS 代码是完整 Visual Studio 产品的劣质版本，但事实并非如此。

VS 代码是一个完全不同的项目。它是使用一套不同的技术构建的，这带来了一些 full VS 没有的功能，例如易于扩展和定制，并且对资源的要求也更低。

VS 代码提供了通过安装扩展来扩展其功能的能力。对于 C#你应该安装 [C#扩展](https://code.visualstudio.com/docs/languages/csharp)。不过，你不需要费心去寻找它。当您打开具有 C#的项目时，如果您还没有 C#扩展，Visual Studio 代码将提示您安装它。

在这篇博文的其余部分，我们将创建一个假设的 ASP.NET 核心 web 应用程序，以说明在. Net 中开发时所需的大多数任务，即如何创建项目和解决方案、引用项目、安装 nuget 包、使用 dotnet 工具(如实体框架)以及运行测试。

## 典型项目

我们将要创建的假想项目是一个提醒应用程序。这里的想法是描述一个足够复杂的项目，需要多个相互引用的项目，一个解决方案文件和测试。

该解决方案将包含一个 ASP.NET 核心项目，例如，可以用来查看过去和未来的提醒。一个控制台应用程序，这样我们就可以从命令行创建提醒(并说明我们如何拥有一个包含多个运行项目的解决方案)和一个包含所有公共逻辑的类库。我们还将添加一个测试项目来说明如何从命令行和 VS 代码内部运行单元测试。

### 创建项目和解决方案文件

这时候你应该已经安装了[。Net 核心](https://www.microsoft.com/net/download/linux)和 [Visual Studio 代码](https://code.visualstudio.com/)。您应该能够打开终端窗口并键入

```
$ dotnet 
```

Enter fullscreen mode Exit fullscreen mode

并得到类似这样的输出:

```
Usage: dotnet [options]
Usage: dotnet [path-to-application]

Options:
-h|--help            Display help.
--version         Display version.

path-to-application:
The path to an application .dll file to execute. 
```

Enter fullscreen mode Exit fullscreen mode

dotnet 命令的选项之一是“新建”。要检查该选项和其他选项，只需进入`dotnet [option] --help`，在本例中为`dotnet new --help`。

这将显示一个可供选择的项目模板列表。例如，创建一个 ASP.NET 核心 MVC 项目:

```
$ dotnet new mvc 
```

Enter fullscreen mode Exit fullscreen mode

如果您照现在这样运行，一个新的 MVC 项目将在当前文件夹中创建，使用当前文件夹的名称作为默认的名称空间和项目名称。

你可能不想那样。如果没有，可以使用`--name`和`--output`选项。使用`--name`(或`-n`)您可以指定默认的名称空间，使用`--output`(或`-o`)您可以指定要创建的文件夹名称以及放置项目文件的位置。如果使用`--name`而不指定`--output`，输出文件夹将隐式地采用与`--name`相同的值。

在我们继续之前，定义项目文件夹结构可能是一个好主意。这是:

```
 Reminders
   +---+ Reminders.Web
   +---+ Reminders.Cli
   +---+ Reminders.Common
   +---+ Reminders.Tests 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做的第一件事是创建一个名为 Reminders 的项目文件夹，然后在里面做:

```
$ dotnet new mvc --name Reminders.Web
$ dotnet new console --name Reminders.Cli
$ dotnet new classlib --name Reminders.Common
$ dotnet new xunit --name Reminders.Tests 
```

Enter fullscreen mode Exit fullscreen mode

这将创建 4 个项目，一个 MVC，一个控制台，一个类库和一个 xUnit 测试项目。

创建一个解决方案文件也是一个好主意。尤其是如果您最终想要在完整的 Visual Studio 中打开这些项目，它会提示您创建一个项目，直到您最终完成。

有一个解决方案文件比避免整个 Visual Studio 唠叨您创建。sln 文件。如果你有一个，你可以进入提醒文件夹，做一个`dotnet build`或`dotnet restore`，然后构建/恢复所有在。sln 文件。

此外，如果您有一个`.sln`文件，您可以通过在`.sln`文件所在的文件夹中启动 VS 代码来打开 VS 代码中的所有项目。

让我们创造我们的。sln 文件，将其命名为 Reminders.sln，并将四个项目添加到其中(在 Reminders 文件夹中):

```
$ dotnet new sln --name Reminders
$ dotnet sln add Reminders.Web/Reminders.Web.csproj
$ dotnet sln add Reminders.Cli/Reminders.Cli.csproj
$ dotnet sln add Reminders.Common/Reminders.Common.csproj
$ dotnet sln add Reminders.Tests/Reminders.Tests.csproj 
```

Enter fullscreen mode Exit fullscreen mode

## 添加项目引用

在完整的 Visual Studio 中，当您想要添加对项目的引用时，只需右击引用并选择添加引用，选择您想要添加的项目，就大功告成了。

VS 代码没有这个功能。要做到这一点，我们需要求助于命令行，但这同样简单。比如让我们参考提醒。常见于提醒。网络，提醒。Cli 和提醒。测试

导航到。sln 文件是(提醒)和类型:

```
$ dotnet add Reminders.Web/Reminders.Web.csproj reference Reminders.Common/Reminders.Common.csproj
$ dotnet add Reminders.Cli/Reminders.Cli.csproj reference Reminders.Common/Reminders.Common.csproj
$ dotnet add Reminders.Tests/Reminders.Tests.csproj reference Reminders.Common/Reminders.Common.csproj 
```

Enter fullscreen mode Exit fullscreen mode

如果您想查看特定项目引用了哪些其他项目，您可以打开。csproj 文件，并查看一下`ProjectReference`条目，或者如果您想从命令行进行查看:`dotnet list PathToCsProj reference`。

您还可以导航到特定项目所在的文件夹，然后简单地执行`dotnet add reference pathToOtherProject.csprj`。

## 挑选启动项目

在 VS 代码中打开解决方案。最简单的方法是导航到“提醒”文件夹。sln 文件是)并键入`code .`。

当您这样做时，您应该在 VS 代码中得到一条消息:“构建和调试所需的资产在‘Reminders’中缺失。加他们？”

单击是。

这将创建一个`.vscode`文件夹。

在那个文件夹中有两个文件:`launch.json`和`tasks.json`。`launch.json`在 VS 代码中配置当你按 F5 时会发生什么。

对我来说，将运行的项目是提醒。Cli 控制台项目:

```
{
// Use IntelliSense to find out which attributes exist for C# debugging
// Use hover for the description of the existing attributes
// For further information visit https://github.com/OmniSharp/omnisharp-vscode/blob/master/debugger-launchjson.md
"version": "0.2.0",
"configurations": [
        {
            "name": ".NET Core Launch (console)",
            "type": "coreclr",
            "request": "launch",
            "preLaunchTask": "build",
            // If you have changed target frameworks, make sure to update the program path.
            "program": "${workspaceFolder}/Reminders.Cli/bin/Debug/netcoreapp2.0/Reminders.Cli.dll",
            "args": [],
            "cwd": "${workspaceFolder}/Reminders.Cli",
            // For more information about the 'console' field, see https://github.com/OmniSharp/omnisharp-vscode/blob/master/debugger-launchjson.md#console-terminal-window
            "console": "internalConsole",
            "stopAtEntry": false,
            "internalConsoleOptions": "openOnSessionStart"
        },
        {
            "name": ".NET Core Attach",
            "type": "coreclr",
            "request": "attach",
            "processId": "${command:pickProcess}"
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，`tasks.json`将只编译提醒。Cli 项目:

```
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "command": "dotnet",
            "type": "process",
            "args": [
                "build",
                "${workspaceFolder}/Reminders.Cli/Reminders.Cli.csproj"
            ],
            "problemMatcher": "$msCompile"
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

可以定制这两个文件，这样您就可以选择运行哪个项目。在这种情况下，我们希望能够运行任一提醒。Web 或 Reminders.Cli。

您应该做的第一件事是删除`tasks.json`的 args 数组中的第二项:" ${workspaceFolder}/Reminders "。CLI/提醒。Cli.csproj "。我们不需要它，因为我们有解决方案文件(Reminders.sln)。当在包含 sln 文件的文件夹中执行`dotnet build`时，解决方案中引用的所有项目都会被构建。

之后，我们现在可以转到`launch.json`并点击“添加配置...”左下角的按钮。

[![Add Configuration... button](../Images/cb0ff3e2baa4b05c7e9c666682ace1f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--llnnMXdO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/02/add_configuration_vscode.png)

选择”。Net:推出一个本地。NET 核心 Web 应用程序”并在生成的 json 中将“程序”和“cwd”(当前工作目录)改为:

```
"program": "${workspaceRoot}/Reminders.Web/bin/Debug/netcoreapp2.0/Reminders.Web.dll",
"cwd": "${workspaceRoot}/Reminders.Web" 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以转到 Visual Studio 代码的调试“部分”,在那里查看 Web 和控制台应用程序配置:

[![Select configuration](../Images/33e704990aa7cc584f9b3c8d0573dc6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DyPQw3yM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/02/select_vscode_configuration_f5.png)

注意，如果愿意，您可以更改配置名称，只需更改`launch.json`中的“name”属性。

现在，当您在 VS 代码中按 F5 时，将运行的项目是在 VS 代码的调试部分中选择的项目。

虽然这只影响 VS 代码，但是`dotnet run`命令不受这些变化的影响。在终端中，如果您导航到解决方案的文件夹并键入`dontet run`,您将得到一个错误(找不到要运行的项目...).您需要使用`--project`参数并指定您想要运行的 csproj 文件的路径，例如:`dotnet run --project Reminders.Web/Reminders.Web.csproj`。或者，你可以导航到项目的文件夹并在那里执行`dontet run`。

## 添加类和接口

在 full Visual Studio 中，当向项目添加新文件时，有几个模板可用。

在 VS 代码中，唯一现成的选项是创建一个新文件并使用 VS 代码片段(例如，键入 class，然后键入 TAB)。

幸运的是，有一个名为[“C#扩展”](https://marketplace.visualstudio.com/items?itemName=jchannon.csharpextensions)的 VS 代码扩展，它在 VS 代码的资源管理器视图的上下文菜单中添加了两个选项:“新 C#类”和“新 c#接口”。

该扩展还增加了从属性生成类的构造函数、从构造函数生成属性、添加只读属性并初始化它们的能力(在该扩展的页面中有相关的[演示)。](https://marketplace.visualstudio.com/items?itemName=jchannon.csharpextensions)

如果不提到一个目前被放弃的替代品，名为[约曼](http://yeoman.io/)，特别是[发电机-aspnet](https://github.com/OmniSharp/generator-aspnet) ，这一节就不完整。

Yeoman 是一个 nodejs 应用程序，允许您安装生成代码的 yeoman 生成器。对于 aspnet 核心，有一个特别有趣的生成器，名为 generator-aspnet。

该生成器提供了一组类似于`dotnet new`现在所提供的项目模板(mvc 应用程序、控制台应用程序等)。但这并不是 generator-aspnet 最有趣的地方。那是它的一组*子发电机*。

yeoman 中的子生成器是一个小型的生成器，用于创建一个文件，例如一个类、一个接口或者一个空的 html 页面。下面是一个使用 generator-asp 创建新类的例子:`yo aspnet:class ClassName`

Generator-asp 有很多这样的例子:

```
yo aspnet:angularcontroller [options] <name>
yo aspnet:angularcontrolleras [options] <name>
yo aspnet:angulardirective [options] <name>
yo aspnet:angularfactory [options] <name>
yo aspnet:angularmodule [options] <name>
yo aspnet:appsettings [options]
yo aspnet:bowerjson [options]
yo aspnet:class [options] <name>
yo aspnet:coffeescript [options] <name>
yo aspnet:dockerfile [options]
yo aspnet:gitignore [options]
yo aspnet:gruntfile [options]
yo aspnet:gulpfile [options] <name>
yo aspnet:htmlpage [options] <name>
yo aspnet:interface [options] <name>
yo aspnet:javascript [options] <name>
yo aspnet:json [options] <name>
yo aspnet:jsonschema [options] <name>
yo aspnet:middleware [options] <name>
yo aspnet:mvccontroller [options] <name>
yo aspnet:mvcview [options] <name>
yo aspnet:nuget [options]
yo aspnet:packagejson [options]
yo aspnet:program [options]
yo aspnet:startup [options]
yo aspnet:stylesheet [options] <name>
yo aspnet:stylesheetless [options] <name>
yo aspnet:stylesheetscss [options] <name>
yo aspnet:taghelper [options] <name>
yo aspnet:textfile [options] <name>
yo aspnet:tfignore [options]
yo aspnet:typescript [options] <name>
yo aspnet:typescriptconfig [options] <name>
yo aspnet:typescriptjsx [options] <name>
yo aspnet:usersecrets [options] <name>
yo aspnet:webapicontroller [options] <name> 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，它们在 0.3.0 版中被删除了。此列表来自`generator-asp@0.2.6`(截至今日的最新版本，2018 年 2 月 25 日为 0.3.3)。

从关于[generator-aspnet github issues](https://github.com/OmniSharp/generator-aspnet/issues)的谈话来看，虽然从未明确提及，但似乎 dotnet new 是今后的必由之路。不幸的是，与 dotnet new 中的这些子生成器相似的唯一生成命令是 *Nuget Config* 、 *Web Config* 、 *Razor Page* 、 *MVC ViewImports* 和 *MVC Viewstart* (当您键入 dotnet new - help 时会得到它们的列表)。

您仍然可以安装这个特定版本的 generator-aspnet(NPM install[generator-aspnet @ 0 . 2 . 6](mailto:generator-aspnet@0.2.6))并使用它(一些生成器仍然有用)。

然而，对于创建类来说，前面提到的扩展 I [是最容易和最方便使用的。让我们用它在提醒中创建一个提醒类。共同项目。](https://marketplace.visualstudio.com/items?itemName=jchannon.csharpextensions)

Reminder 类有一个 Id 属性、一个描述、一个日期和一个 boolean 标志，指示提醒已被确认。

你需要做的第一件事是安装[*C #扩展*扩展](https://marketplace.visualstudio.com/items?itemName=jchannon.csharpextensions)。然后右击提醒。在资源管理器中查看并选择新建类:

[![New Class](../Images/f77267bac543c613db996069053a8f57.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TXDjERbR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/03/new_class_reminders.png)

将其命名为 Reminder.cs 并创建三个属性。最后，它应该是这样的:

```
using System;

namespace Reminders.Common
{
    public class Reminder
    {
        public int Id { get; set; }
        public string Description { get; set; }
        public DateTime Date { get; set; }
        public bool IsFinished { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在将光标放在课程提醒后的开始“{”处，单击灯泡并选择“从属性初始化 ctor”:

[![Initialize constructor form properties](../Images/d4c2254034343301d41b8eaff11f5183.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_eFFu71k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/03/initialise_ctor_from_properties_reminders.png)

您应该得到这样的结果:

```
using System;

namespace Reminders.Common
{
    public class Reminder
    {
        public Reminder(int id, string description, DateTime date, bool isFinished)
        {
            this.Id = id;
            this.Description = description;
            this.Date = date;
            this.IsFinished = isFinished;

        }
        public int Id { get; set; }
        public string Description { get; set; }
        public DateTime Date { get; set; }
        public bool IsFinished { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

尽管构造函数并不是真正必要的(如果你打算使用它，它将不能很好地与实体框架一起使用)，但它只是你从扩展中得到的一些好特性的一个例子。此外，在定义属性时，您可以使用完整 Visual Studio 中提供的相同代码段(即，键入 prop 并按 TAB)。

## 剃刀观点

剃刀页(。cshtml)绝对是缺乏 VS 代码的领域。没有智能感知，甚至没有自动缩进。

市场上有一个名为[ASP.NET 助手](https://marketplace.visualstudio.com/items?itemName=schneiderpat.aspnet-helper)的扩展，它可以让你获得智能感知，但是你必须在`_ViewImports.chtml`中为你的模型导入名称空间(扩展的需求在扩展页面的底部)。同样，在其当前版本中，只有当您使用的模型与视图在同一个项目中时，它才起作用。

这个扩展在某些情况下很有用，但是非常有限。

如果这对你来说是一个交易破坏者，这里有一个关于为 VS 代码所依赖的语言服务添加 Razor 支持的 [github 问题](https://github.com/OmniSharp/omnisharp-vscode/issues/168)，它将提供类似于 Visual Studio 完整版本的智能感知。如果你投赞成票或在那里留下评论，这将带来这个问题的可见性。

如果你想在今天创建剃刀网页的时候有一个好的体验，并且你不介意支付一点钱，你可以试试 JetBrains Rider。Rider 可以在 Windows、Mac 和 Linux 上运行，它是由创建 ReSharper 的同一批人开发的。几个月前它还在测试阶段的时候我就试过了。当时，它需要一台相当不错的机器才能平稳运行，而且缺少一些功能。我今天在写这篇文章的时候又试了一次，感觉好多了。剃刀支架看起来很完美。

即使现在在 VS 代码中使用 Razor 的体验并不理想，如果你用一个前端 JavaScript 框架(Angular，React，Vue 等)代替 Razor，你会发现 VS 代码非常优秀。我主要使用 Angular 和 TypeScript，这是 VS 代码比 Visual Studio 的完整版本更好的地方。

如果你对 Angular 有所了解，并计划使用 ASP.NET 核心作为你前端的 Web Api，请查看我的另一篇文章 [Angular 和 ASP.NET 核心](https://www.blinkingcaret.com/2018/01/24/angular-and-asp-net-core/)。

## 获取数据包

在 Visual Studio 的完整版本中，有一个管理 NuGet 包的选项。你得到一个用户界面，在那里你可以搜索、更新和安装 NuGet 包。在 VS 代码中，不支持 NuGet out of the box。

幸运的是，你可以安装一个扩展来搜索、添加和删除 VS 代码中的 NuGet 包。这个扩展被命名为 [NuGet 包管理器](https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager)。

或者，您可以使用命令行。向项目添加包的语法是:`dotnet add PathToCsproj package PackageName`。或者，如果您导航到您想要添加 NuGet 包的项目，您可以省略 csproj 文件的路径:`dotnet add package PackageName`。

假设您想将`Microsoft.EntityFrameworkCore`包添加到提醒中。公共类库。导航到它后:

```
$ dotnet add package Microsoft.EntityFrameworkCore 
```

Enter fullscreen mode Exit fullscreen mode

## 添加工具

CLI 工具的一个例子是实体框架的工具。当您输入一个安装了 EF 工具的项目时，它会运行:

```
$ dotnet ef 
```

Enter fullscreen mode Exit fullscreen mode

您应该得到这样的输出:

```
 _/\__
       ---==/    \\
___  ___   |.    \|\
| __|| __|  |  )   \\\
| _| | _|   \_/ |  //|\\
|___||_|       /   \\\/\\

Entity Framework Core .NET Command Line Tools 2.0.1-rtm-125

Usage: dotnet ef [options] [command] 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，没有自动配置 dotnet 工具的方法。如果您从一个没有正确设置的项目模板开始，您将不得不手动编辑。csproj 文件。

不仅如此，你还必须知道正确的包名和版本。

这不是特定于 VS 代码或者 Linux 的问题。这是一个工装问题。下面是 github 中的相关问题: [dotnet 添加包不尊重包类型](https://github.com/dotnet/cli/issues/5998)和[VS 2017:package type = DotnetCliTool 安装失败](https://github.com/NuGet/Home/issues/4190)

不过，有一种方法可以手动完成。如果你打开一个. csproj 文件，你会注意到它有`ItemGroup`部分。例如，对于一个新的 mvc 项目，是这样的:

```
<ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />        
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

对于这个工具，你应该创建另一个`ItemGroup`，并在其中使用`DotNetCliToolReference`而不是`PackageReference`(在你的项目中可能已经有一个带有 DotNetCliToolReference 的 ItemGroup，如果是这样的话，就添加到它里面)。

假设我们想在 web 项目中使用实体框架工具。导航至`Reminders.Web`并打开 csproj 文件。

记下`Microsoft.AspNetCore.All`包的版本。比如我们假设是“2.0.0”。

创建一个新的`ItemGroup`(或者找到已经有`DotNetCliToolReferences`的项目组)并在里面为带有`Version="2.0.0"`的`Microsoft.EntityFrameworkCore.Tools.DotNet`添加`DotNetCliToolReference`。

```
<ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />            
</ItemGroup>
<ItemGroup>
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" /> <!-- ADD THIS -->
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />        
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该可以在提醒中运行`dotnet ef`。Web 项目。

版本必须与“Microsoft。AsNetCore.All”是因为该包是元包，即只引用其他包的包。其中一些包是实体框架包，如果版本不匹配，这将导致工具的兼容性问题。

如果我们在一个控制台项目中安装实体框架核心工具，你不需要担心版本。[然而，除了手动添加`DotNetCliToolReference`](https://docs.microsoft.com/en-us/ef/core/miscellaneous/cli/dotnet#installing-the-tools) 之外，你还必须添加包`Microsoft.EntityFrameworkCore.Design`。

## 运行测试

您的开发工作流可能涉及的一件事是编写单元测试。

VS 代码提供了对运行单元测试的现成支持。例如，如果您打开 UnitTest1.cs，您会注意到在测试方法上有两个链接，运行测试和调试测试:

[![run test, debug test](../Images/665df27242caed2aa76d2e3e7461fb3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w_9V9ngC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.blinkingcaret.com/wp-content/uploads/2018/03/run_test_debug_test.png)

这将允许你一次运行一个单元测试，在 VS 代码中没有办法运行一个项目中的所有测试。但是，您可以在终端中导航到一个测试项目，并键入:

```
$ dotnet test 
```

Enter fullscreen mode Exit fullscreen mode

这是你应该得到的输出，如果你这样做是为了提醒。测试:

```
Starting test execution, please wait...
[xUnit.net 00:00:00.4275346]   Discovering: Reminders.Tests
[xUnit.net 00:00:00.5044862]   Discovered:  Reminders.Tests
[xUnit.net 00:00:00.5549595]   Starting:    Reminders.Tests
[xUnit.net 00:00:00.6980509]   Finished:    Reminders.Tests

Total tests: 1\. Passed: 1\. Failed: 0\. Skipped: 0.
Test Run Successful. 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以指定您想要运行的测试项目的路径，例如`dotnet test Reminders.Tests/Reminders.Tests.csproj`。

还可以在命令行中指定要运行的测试，例如，假设您只想运行 MyClassTests 类中的测试。你可以这样做:

```
$ dotnet test --filter MyClassTests 
```

Enter fullscreen mode Exit fullscreen mode

过滤功能有[多个选项](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-test?tabs=netcore2x#filter-option-details)。上例是`dotnet test --filter FullyQualifiedName~MyClassTests`的简称(~表示包含)。

还有其他方便的选项，例如在 xUnit 中可以为测试指定“特征”,例如:

```
[Trait("TraitName", "TraitValue")]
public class UnitTest1
{
    [Fact]        
    public void Test1()
    {
        //...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以运行:

```
$ dotnet test --filter TraitName=TraitValue 
```

Enter fullscreen mode Exit fullscreen mode

只有具有这种特征的测试才会运行。您可以在方法或类级别指定`Trait`。

[虽然 trait 是特定于 xUnit](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-test?tabs=netcore2x#filter-option-details) 的，但是在其他测试框架上有等价的构造。比如 mstest 有`TestCategory`。

如果你喜欢在 VS 代码中做所有的事情，你可以为运行测试创建任务。首先转到`tasks.json`并复制一份构建任务，将它的标签改为 test(或者你想叫它什么)并更新 args，这样 test 就不用构建，而是使用测试项目的路径来执行，例如为了提醒。测试:

```
 {
        "label": "test",
        "command": "dotnet",
        "type": "process",
        "args": [
            "test",
            "${workspaceFolder}/Reminders.Tests/Reminders.Tests.csproj"
        ],
        "problemMatcher": "$msCompile"
    } 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用命令面板(Ctrl + Shift + P)并键入“Run Task ”,然后选择您想要运行的任务，从而在 VS 代码中运行任务。您甚至可以将一个任务设置为“测试任务”，并为其分配一个键盘快捷键(与您可以通过 Ctrl + Shift + B 来触发配置为“构建任务”的任务的方式相同)。

要将一个任务设置为“测试任务”，打开命令面板，选择“任务:配置默认测试任务”，选择测试任务，就大功告成了。

## 最后一个提示

有时 VS 代码(实际上是 Omnisharp)似乎会失控，例如 intellisense 停止工作。当这种情况发生时，你可以通过使用命令面板并选择“Reload Window”来重新加载 VS 代码，或者不太激进的选择是重新启动 Omnisharp(命令名是:Omnisharp: Restart Omnisharp)。

希望这个指南对你有帮助，请在评论中告诉我。