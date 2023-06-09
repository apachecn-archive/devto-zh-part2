# 如何在 macOS 上 dotnet 构建和定位 net4x

> 原文：<https://dev.to/stuartblang/how-to-dotnet-build-and-target-net4x-on-macos-3h0h>

在[之前的一篇文章](https://dev.to/stuartblang/net-framework-support-for-net-standard-20-5a4i-temp-slug-9523411)中，我建议在某些场景中除了`netstandard2.0`之外，还可以额外锁定`net461`，然而锁定`netstandard2.x`的好处之一是在 macOS 上你可以安装。NET Core SDK，构建它，“它就是工作”。

然而，当你用`net4x`运行`dotnet build`时，你可能会看到这个错误:

```
error MSB3644: The reference assemblies for framework ".NETFramework,Version=v4.6.1" were not found. 
```

这很有意义，因为在这种情况下，为了编译`net461`，我们需要该框架的引用程序集，这些是与运行时程序集相同的 dll，但只有签名，没有实现。在 Windows 上，这些包被称为“目标包”，您可以将这些包与 Visual Studio 一起提供，或者通过称为“开发人员包”的独立安装程序打包。

据我所知，在 macOS 上，有两种方法可以引用这些引用程序集；

### 1。使用单声道参考组件

如果你安装了 Visual Studio for Mac 或 Rider，那么你可能已经安装了，否则，从这里安装最新的稳定版本是个好主意。您可能能够在`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono`中的版本文件夹下找到引用程序集。

现在，附带的 MSBuild。NET Core SDK 需要知道在哪里可以找到引用程序集。我在这里找到了一个[解，但是，我不确定它来自哪里(可能是来自那里？).](https://github.com/dotnet/sdk/issues/335#issuecomment-368669050)

您可以将一个名为`netfx.props`的文件包含[这个内容](https://github.com/Microsoft/visualfsharp/blob/24a432f03219b62fa647777a531bb7e70fa044e0/fcs/netfx.props)，并将其导入到您的项目中，如下所示:

```
<Project Sdk="Microsoft.NET.Sdk">
  <Import Project="netfx.props" />
  <PropertyGroup>
    <Description>... 
```

现在你可以走了！

### 2。通过 MyGet 获取引用程序集

另一种不需要安装 mono 的方法是通过 nuget 获得它们，然而，它们目前在 nuget.org 还不可用，对此有一个 GitHub 问题可以跟踪。它们目前存在于 MyGet 提要中(从版本 4.5 开始)。这些是微软的官方软件包，[这里有一个 4.6.1](https://dotnet.myget.org/feed/dotnet-core/package/nuget/Microsoft.TargetingPack.NETFramework.v4.6.1) 的例子。

首先，您需要像这样添加提要:

```
 <PropertyGroup>
    <TargetFrameworks>net461;netstandard2.0</TargetFrameworks>
    <RestoreAdditionalProjectSources>
      https://dotnet.myget.org/F/dotnet-core/api/v3/index.json
    </RestoreAdditionalProjectSources>
  </PropertyGroup> 
```

你可能也见过通过`NuGet.config`完成的(只是没那么酷)，或者你可能见过使用`RestoreSources`属性完成的，这需要你像这样把它附加到现有的源代码:`<RestoreSources>$(RestoreSources);...</RestoreSources>`

你可以在这里阅读更多关于[的内容。](https://github.com/NuGet/Home/wiki/%5BSpec%5D-NuGet-settings-in-MSBuild#project-properties)

现在我们可以导入包:

```
 <ItemGroup Condition=" '$(TargetFramework)' == 'net461' ">
    <PackageReference Include="Microsoft.TargetingPack.NETFramework.v4.6.1"
                        Version="1.0.1" ExcludeAssets="All" PrivateAssets="All" />
  </ItemGroup> 
```

最后，我们需要将`FrameworkPathOverride`指向这个包中的程序集:

```
 <PropertyGroup Condition="'$(TargetFramework)'== 'net461'">
      <FrameworkPathOverride>$(NuGetPackageRoot)microsoft.targetingpack.netframework.v4.6.1/1.0.1/lib/net461/</FrameworkPathOverride>
  </PropertyGroup> 
```

### 结论

仅此而已。总是可以选择使用 mono 附带的`msbuild`,只为`net4x` tfm 运行它，而为其他 tfm 使用`dotnet build`,但是这有什么意思呢？让我知道你是否有其他的方法在评论中解决这个问题。