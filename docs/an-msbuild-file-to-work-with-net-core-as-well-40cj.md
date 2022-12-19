# 要使用的 MSBuild 文件。也是网络核心

> 原文：<https://dev.to/olddutchcap/an-msbuild-file-to-work-with-net-core-as-well-40cj>

我有时会花费一些额外的时间和精力来从头创建一些文件，只是因为我不想让自动化工具构建文件带来的所有无关紧要的麻烦。例如，如果我在 Visual Studio 中创建了一个新的解决方案，那么在这个过程中会创建一个. csproj 文件。还有那个。csproj 将充满各种我不想要或不关心的无关设置。所以我时不时地构建一个 MSBuild。csproj 从零开始。

我有一个 MSBuild 文件，可以很好地构建我创建的小型/简单的库。但是当我试着用 dotnet core 构建这个库时，我遇到了一个问题。

深入研究这个问题，我发现基本上 MSBuild 做了一些隐藏的魔法来包含一些文件，而没有被告知要包含它们。具体来说，它包括了 mscorlib.dll，但文件从未被提及。但是 dotnet core 没有那种隐含的行为。

因此，没有任何进一步的麻烦，这里是我的修改后的构建文件，它可以与 MSBuild 和 dotnet build 一起工作。希望对别人有帮助。

```
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <ItemGroup> 
    <Compile Include="ColorNumberGameEvent.cs" /> 
    <Compile Include="GameStates.cs" />
    <Compile Include="LegalColors.cs" />
    <Compile Include="GameSolution.cs" />
  </ItemGroup>
  <ItemGroup>
    <References Include="C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v3.5\Profile\Client" />  <!-- Included for dotnet build -->
  </ItemGroup>                        
  <Target Name="Build">
    <Csc TargetType="library" Sources="@(Compile)"
       TreatWarningsAsErrors="true"
       WarningLevel="4"
       AdditionalLibPaths="@(References)"
       References="mscorlib.dll"/> <!-- Included for dotnet build -->
  </Target>
</Project> 
```

Enter fullscreen mode Exit fullscreen mode

几个注意事项:

1.)我会使用 *ProgramFiles(x86)* 环境变量(而不是硬编码路径),但是 MSBuild 和环境变量有一个错误，其中包含“("

2.)你需要设置*额外的库路径*和*引用*来使其工作。