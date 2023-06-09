# dotnet-cleanup:清理解决方案、项目和文件夹

> 原文：<https://dev.to/sebnilsson/dotnet-cleanup-clean-up-solution-project-folder-3ef8>

我们开发人员喜欢把开发软件看作一门精确的科学，但是有时候，你只需要清除你的源文件来解决一些问题。

因为。NET 开发者，Stackoverflow 上有很多问题，只要删除你的`bin`和`obj`文件夹就可以解决。对于使用 Node.js 的人来说，可能同样多的答案包含删除你的`node_modules`文件夹的步骤。

这些就是我创建`dotnet-cleanup`的一些原因，它是一个[。NET 核心全球工具](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools)用于清理解决方案，项目或文件夹。遵循 Nate McMaster 关于[开始创建. NET 核心全球工具包](https://natemcmaster.com/blog/2018/05/12/dotnet-global-tools/)的帖子，这变得很容易。

在删除之前，被删除的文件和文件夹首先被移动到一个临时文件夹中，因此**您可以继续使用您的项目**，同时该工具继续在后台清理。

## 安装

下载[。NET Core SDK 2.1](https://aka.ms/DotNetCore21) 或更高版本。 [`dotnet-cleanup`](https://www.nuget.org/packages/dotnet-cleanup) 的安装。NET 全局工具，使用命令行:

```
dotnet tool install -g dotnet-cleanup 
```

## 用法

```
Usage: cleanup [arguments] [options]

Arguments:
  PATH Path to the solution-file, project-file or folder to clean. Defaults to current working directory.

Options:
  -p|--paths Defines the paths to clean. Defaults to 'bin', 'obj' and 'node_modules'.
  -y|--confirm-cleanup Confirm prompt for file cleanup automatically.
  -nd|--no-delete Defines if files should be deleted, after confirmation.
  -nm|--no-move Defines if files should be moved before deletion, after confirmation.
  -t|--temp-path Directory in which the deleted items should be moved to before being cleaned up. Defaults to system Temp-folder.
  -v|--verbosity Sets the verbosity level of the command. Allowed levels are Minimal, Normal, Detailed and Debug.
  -?|-h|--help Show help information 
```

参数`PATH`可以指向一个特定的`.sln`文件或者一个项目文件(`.csproj`、`.fsharp`、`.vbproj`)。如果指定了一个`.sln`-文件，它的所有项目都将被清理。

如果它指向一个文件夹，将扫描该文件夹以查找单个解决方案文件，然后查找单个项目文件。如果检测到多个文件，将显示一个错误，您需要指定文件。

如果找不到解决方案或项目，文件夹将作为项目清理。

### 举例

要清理一个典型的 web 项目，您可以像这样指定项目中要清理的路径:

```
cleanup -p "bin" -p "obj" -p "artifacts" -p "npm_modules" 
```

你可以在 GitHub 上找到[源代码，在 MyGet](https://github.com/sebnilsson/DotnetCleanup) 上找到[最新版本，在 Nuget](https://www.myget.org/feed/sebnilsson/package/nuget/dotnet-cleanup) 上找到[包。](https://www.nuget.org/packages/dotnet-cleanup/)

你可以找到更多的列表。NET Core Global Tools on GitHub ，由 Nate McMaster 维护。