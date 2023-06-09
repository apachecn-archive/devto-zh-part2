# 包含本地库的 Nupkg

> 原文：<https://dev.to/jeikabu/nupkg-containing-native-libraries-1576>

最近开始考虑将我们的 C#库发布到 nuget.org。这第一篇文章涵盖了我对实际包装的体验。在以后的文章中，我会做签名和发布。

## 开始

[Subor。NNanomsg.NETStandard](https://www.nuget.org/packages/Subor.NNanomsg.NETStandard/) ( [我们的分支](https://github.com/zplus/NNanomsg) [NNanomsg](https://github.com/mhowlett/NNanomsg) 的——nanosg[的一个 C#库)尤其令人沮丧，因为它涉及一个本地库`nanomsg.dll`。](https://nanomsg.org/)

谷歌关闭了几个有前景的资源:

*   堆栈溢出 [#1](https://stackoverflow.com/questions/19478775/add-native-files-from-nuget-package-to-project-output-directory) 和 [#2](https://stackoverflow.com/questions/40104838/automatic-native-and-managed-dlls-extracting-from-nuget-package)
*   [MS docs 关于使用 nuget 创建原生包](https://docs.microsoft.com/en-us/nuget/create-packages/native-packages)
*   前 SignalR/ASP 的博客。网佬

但是我努力想做一个有用的包。`nanomsg.dll`不在输出目录中。

## 一般提示

好几个地方提到的`.targets`文件似乎已经没有必要了(VS 15.7.4 和 nuget.exe 4 . 7 . 0)，除非你的包会被 c++项目消费掉。

托管库需要打包到`/lib/TFM/`中。见[支持多个。NET 框架版本](https://docs.microsoft.com/en-us/nuget/create-packages/supporting-multiple-target-frameworks)和关于[目标框架名字(TFM)](https://docs.microsoft.com/en-us/nuget/reference/target-frameworks#supported-frameworks)

本地库需要打包到`/runtimes/RID/native/`中。[运行时 ID 目录](https://docs.microsoft.com/en-us/dotnet/core/rid-catalog)不仅有所有运行时 ID(rid)的完整列表，还给出相当详细的解释。

*   熟悉 nuget.exe
    *   `nuget pack -Properties Configuration=Release`将在当前目录下为`.csproj`构建一个 release nupkg(默认为 debug)。将自动包含输出程序集。
    *   可以通过添加`-Version X.Y.Z`来设置包版本
*   NuGetPackageExplorer 给你的 nupkg 和它的元数据一个很好的视觉外观。只要确保**文件- >关闭**否则 nuget.exe 和 Visual Studio 会有问题访问你的 nupkg。

## Nuspec

*   `nuget.exe spec`将从当前目录下的 csproj 生成一个模板 nuspec
*   从命令行使用 nuget.exe 时，会自动包含一个与 csproj 同名的 nuspec 文件。比如`NNanomsg.NETStandard.csproj`和`NNanomsg.NETStandard.nuspec`。
*   [nu spec 中可用的替换令牌](https://docs.microsoft.com/en-us/nuget/reference/nuspec#replacement-tokens)
*   将 nuspec 与 csproj 一起使用很笨拙
    *   只有一些元数据值的替换标记
    *   从 nuspec 中省略一个标签会导致该值未被定义——即使它是在 csproj 中设置的
    *   在构建时启用 NuGet 包的生成(放在输出文件夹中)不使用 nuspec: ![](img/b53a58e9c813f430f95e0e5a1d8047c9.png)

当使用 nuget.exe 时，nuspec 的`<files>`部分似乎是包含`/runtimes/`文件的唯一方式。csproj 里的`<Content>`把东西放在`content/`子文件夹里(nuget.exe 忽略`<ContentTargetFolders>`)，`<None>`根本不包括。

*   可以直接打包 nuspec(如`nuget pack NNanomsg.NETStandard.nuspec`)
    *   如果您看到以下警告:

```
 WARNING: NU5100: The assembly 'bin\Release\netstandard2.0\NNanomsg.NETStandard.dll' is not inside the 'lib' folder and hence it won't be added as a reference when the package is installed into a project. Move it into the 'lib' folder if it needs to be referenced. 
```

Enter fullscreen mode Exit fullscreen mode

您的 nuspec 中可能需要以下内容:

```
 <files>
      <file src="bin\$configuration$\netstandard2.0\*.dll" target="lib\netstandard2.0\"/>
      <file src="runtimes\**" target="runtimes/"/>
  </files> 
```

Enter fullscreen mode Exit fullscreen mode

*   如果出现以下错误:

```
 Attempting to build package from 'NNanomsg.NETStandard.nuspec'.
  Value cannot be null or an empty string.
  Parameter name: value 
```

Enter fullscreen mode Exit fullscreen mode

nuspec 包含需要用实际值替换的替换标记(即`$xxx$`)

## NuGet 作为 MSBuild 目标

[从 NuGet 4.0 开始，所有与包相关的元数据都可以存储在 csproj](https://docs.microsoft.com/en-us/nuget/reference/msbuild-targets) 中。

如果你的项目没有引用任何外部包，你*可能*需要[通过添加到你的 csproj:
来强制包引用格式](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files#using-packagereference-for-a-project-with-no-packagereferences)

```
<PropertyGroup>
    <RestoreProjectStyle>PackageReference</RestoreProjectStyle>
</PropertyGroup> 
```

Enter fullscreen mode Exit fullscreen mode

不确定是否有关系，但是我默认使用 Package reference(**工具- >选项- > NuGet 包管理器** ):

[![](img/454b64f5334db739bf32a44064dd35a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s----4JRHEG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/vs_options_nuget_package_management.png)

我用:
包含了本机二进制文件

```
<ItemGroup>
    <Content Include="runtimes\**" PackagePath="runtimes" Visible="false" />
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

然后右键点击**项目- >包**生成 nupkg。或者在 VS 开发者命令行提示:

```
msbuild /t:pack 
```

Enter fullscreen mode Exit fullscreen mode

## 消耗 nupkg

在 Visual Studio 中，**工具- >选项- > NuGet 包管理器- >包源**添加一个本地目录作为 NuGet 包的源:

[![](img/b0b02b4939b93be45c3207b23c39fcba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SD5fQ96f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/vs_options_package_source.png)

Visual Studio 包管理器(**视图- >其他窗口- >包管理器控制台**)使得重新加载本地 nupkg 变得很容易。确保**默认项目**设置为正确的项目并运行:`Install-Package D:\dev\NNanomsg\NNanomsg.NETStandard\NNanomsg.NETStandard.0.5.2.nupkg`。

安装软件包不会导致本机二进制文件被复制，您必须构建项目。

假设您的 nupkg 包含 32 位和 64 位的本地库，分别是`runtimes/win-x86/native/nanomsg.dll`和`runtimes/win-x64/native/nanomsg.dll`。如果消费项目的*平台目标*(项目**属性- >构建**)为`Any CPU` **，则**本地程序集都不会被复制到输出目录。您**必须**选择`x86`或`x64` :

[![](img/765aaad5f841ded59be9801c23d3e88e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n9eEIUXn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/vs_project_properties_platform_target.png)

## 结论

*   选择 nuspec 或 VS project 进行包定义最不令人沮丧——不要两个都用
*   Nuspec 减少了 VS 的集成，但是命令行使用更简单——只需要[nuget.exe](https://www.nuget.org/downloads)
*   使用 VS 项目进行包定义具有最好的 VS 集成，但是却无法处理 msbuild 的复杂性

接下来，我会在我们的包裹上签名，并把它推给 nuget.org。