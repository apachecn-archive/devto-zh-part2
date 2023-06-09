# 针对多个框架和机器的构建

> 原文：<https://dev.to/davidwengier/targeting-builds-for-multiple-frameworks-and-machines-5h22>

我最近开始在业余时间做一个新项目，名为 [DbUpgrader](http://github.com/davidwengier/dbupgrader) ，我尝试每天晚上至少花几分钟来做这个项目。我不同地使用 MacBook Pro 或 Windows 机器，有时我使用 Visual Studio 2017，但有时我只是使用 Visual Studio 代码，在控制台上瞎折腾。我还想在不久的将来尝试一下 Visual Studio for Mac。所有这些不同的环境都有它们的优势和特点，但是我最想确定的是我可以在所有的环境中工作，在同一个项目中，没有问题。

进入 Visual Studio 中的[新项目系统](https://github.com/dotnet/project-system)，它允许最小化。csproj 文件仍然是易于编辑的 MSBuild 目标，而不必妥协，并为每个场景提供单独的生成脚本。我给自己设定的挑战是，看看我能否用满足以下需求的项目创建一个单一的解决方案:

*   在 Windows 上的 Visual Studio 中打开，没有错误
*   在 Visual Studio 中构建没有问题
*   测试出现在 Visual Studio 的测试资源管理器中，并且测试按预期运行
*   在 Mac 和 Windows 上使用`dotnet build`
*   在 Mac 和 Windows 上使用`dotnet test`

这看起来很简单，但事实上有点复杂，我不仅要支持完整的。Windows 上的. NET Framework v4.6，但也。NET Core 在 Mac 和 Windows 上，没有。NET 4.6 支持是 Mac 上的一个问题。来支持。NET 4.6 共享库需要。NET Standard 1.3 或更低版本，但是我也有一些功能和测试使用了。NET 标准 2.0，因此与。净 4.6。所以在 Windows 上，我想要一个构建。NET 4.6，但不支持 Sqlite，而。NET 核心，并在 Mac 上为。NET 核心与支持，没有与丢失相关的错误。NET 框架支持。

## 多目标意味着多构建

在新的项目系统中考虑多目标的最简单的方法是记住这个简单的事实:每个目标框架就像整个项目的复制品一样。考虑一个带有以下声明的. csproj 文件。

```
<TargetFrameworks>net46;netcoreapp2.0</TargetFrameworks> 
```

生成此项目时，MSBuild 将运行两次生成，一次用于。NET Framework 4.6 (net46)和一次性。网芯(netcoreapp2.0)。了解这一点有助于解释项目文件应该如何布局的逻辑，以便改变为每个目标构建的内容。

在我的例子中，我希望 Sqlite 代码只为 netcoreapp2.0 构建，因为它需要面向目标。NET 标准 2.0，而 net46 还不太在那个水平。GitHub 上的[提供了完整的版本列表以及它们所支持的内容，但可以说 net46 映射到了。净标准 1.3。](https://github.com/dotnet/standard/blob/master/docs/versions.md)

有了这些信息，我们知道在为 net46 构建时需要排除 Sqlite 依赖项和文件，这是通过在项目文件的相关位置使用一个`Condition`属性来完成的。

```
<ItemGroup Condition="'$(TargetFramework)' == 'netcoreapp2.0'">
  <ProjectReference Include="..\..\src\DbUpgrader.Sqlite\DbUpgrader.Sqlite.csproj" />
</ItemGroup> 
```

在这里，我指示项目系统仅在构建的目标框架是 netcoreapp2.0 时引用 Sqlite 项目。这是将目标视为独立构建有意义的地方。当它通过这个文件时，MSBuild 会发现不满足条件，并简单地跳过文件的这一部分。将不添加任何引用。为 netcoreapp2.0 构建时，将添加引用。

## 排除文件

这对于引用来说很好，但是很明显，如果引用在那里，那么一定会有文件使用它。因为新的项目系统不需要特定的文件包含，所以不太可能有一个节点可以添加条件，所以我们需要有点创意。

你可以在普通的`include`旁边的`<Compile>`元素上使用一个`exclude`属性，但是我发现这种用法有点难看，因为默认情况下 Sdk 项目中不需要任何`<Compile>`元素，所以再添加一个似乎有点笨拙。我选定的解决方案是简单地更新已经存在的`DefaultItemExcludes`属性，并且已经被默认项目使用。新系统中的 glob 支持也使这变得轻而易举，只需要一个简单的添加就可以排除多个文件和文件夹/子文件夹。

```
<PropertyGroup Condition="'$(TargetFramework)' == 'net46'">
  <DefaultItemExcludes>$(DefaultItemExcludes);Integration\Sqlite\**\*</DefaultItemExcludes>
</PropertyGroup> 
```

因为我们现在告诉 MSBuild*排除我们不想要的*项，所以我们翻转条件，使其基于 net46。这两件事结合在一起意味着我们得到的项目包括我们在构建时想要的一切。NET 核心，并且在为。NET 框架。

## 瞄准目标

如果到目前为止，条件都是基于目标框架的，那么如何让目标有条件呢？要做到这一点，你需要更高层次的东西，幸运的是，操作系统完美地扮演了这个角色。我们可以告诉 MSBuild 进行构建。网芯和。Windows 上的. NET 框架，就。NET Core，一切都将基于当时正在构建的目标正确地从那里开始。条件看起来也很相似。

```
<TargetFrameworks>netcoreapp2.0</TargetFrameworks>
<TargetFrameworks Condition="'$(OS)' != 'Unix'">net46;netcoreapp2.0</TargetFrameworks> 
```

这里要注意两点:第一点是 Mac 的 OS 是“Unix”。这让我很惊讶，但不是什么大事。我最初猜测它会是“Mac ”,当这不起作用时，我简单地将一个构建任务添加到我的项目文件中，并观察输出是什么。任务如下，它通过在`<Project>`节点中指定`InitialTargets="LogDebugInfo"`来运行，但是它再次很好地提醒了我们，这些 csproj 文件也只是 MSBuild 脚本，并且可以被当作 MSBuild 脚本来处理——尽管事后复查 Visual Studio 仍然很高兴。

```
<Target Name="LogDebugInfo">
  <Message Text="Building for $(TargetFramework) on $(OS)" Importance="High" />
</Target> 
```

其次，你会注意到其中一个元素只有一个条件。这不是我首先尝试的，因为我假设在没有条件区分它们的情况下复制元素会有问题。事实上，虽然在两者上都有条件在`dotnet build`世界(Mac 和 Windows)中运行良好，但 Visual Studio 本身却变得非常混乱。我在 Twitter 上发布了这件事，在微软新项目系统[工作的](https://twitter.com/davkean/status/987820416579223552)[大卫·基恩](https://twitter.com/davkean)非常有帮助，他把我指向[这个 GitHub 问题](https://github.com/dotnet/project-system/issues/1829)解释说我遇到了一个错误。去掉一个条件没什么大不了的，我只要确保顺序正确就行了。拥有两个`<TargetFrameworks>`元素意味着第二个元素覆盖了第一个元素，所以为了让 Windows 仍然支持 net46，它必须排在最后。

看起来只要项目文件中有一个没有条件的元素，Visual Studio(至少是我正在尝试的 v15.6.7)就很高兴，尽管我怀疑 IDE 认为我是为其开发的。仅限网络核心。然而，当从 Visual Studio 进行构建时，由于它只运行 MSBuild，所以没有问题。理论上，这可能意味着 IDE 可以将某些东西标记为正确的，然后让构建失败，反之亦然，但这是为灵活性付出的微小代价，我认为这只是一个暂时的问题，直到构建被修复。

## 比起你的旧东西，我更喜欢你的新东西

总的来说，新的项目系统很棒，我喜欢能够在 Visual Studio 中打开项目文件时对其进行编辑，并看到更改立即生效。总的来说，让人们思考项目文件和构建文件是一件好事，因为这鼓励了“devops 心态”，我个人是这种心态的粉丝，我认为每个开发人员都应该努力实现这种心态。

但那是另一次的评论。