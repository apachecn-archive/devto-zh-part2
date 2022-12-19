# 使用新的 SDK 项目和 MSBuild 添加 AssemblyMetadataAttribute

> 原文：<https://dev.to/stuartblang/adding-assemblymetadataattribute-using-new-sdk-project-with-msbuild-2akl>

现在我们有了新的项目系统，并且我们可以在我们的`.csproj`中定义通用的汇编信息，我们可以和`AssemblyInfo.cs`说再见了，嗯，我仍然看到这些是通用汇编属性。

例如，现在很常见的是看到一个`InternalsVisibileTo.cs`文件，而不是`AssemblyInfo.cs`，[见这里的例子](https://github.com/aspnet/EntityFrameworkCore/blob/cfed64d50977fc7c85267ddaab0aa96d834776cd/src/EFCore/Properties/InternalsVisibleTo.cs)。

但是，如果您想添加自己的程序集属性，并希望将该属性的值作为 MSBuild 属性传入，该怎么办呢？例如，包含 git 提交散列的`AssemblyMetadataAttribute`？

嗯，我们很幸运，有一个`AssemblyAttribute`物品我们可以添加到我们的物品组！如果我们有一个名为`CommitHash`的属性，我们可以这样写:

```
<AssemblyAttribute Include="System.Reflection.AssemblyMetadataAttribute" Condition="$(CommitHash) != ''" >
    <_Parameter1>CommitHash</_Parameter1>
    <_Parameter2>$(CommitHash)</_Parameter2>
</AssemblyAttribute> 
```

太好了！最后一件事，我们需要说这什么时候会发生，那是在`CoreGenerateAssemblyInfo`之前，所以:

```
<Target Name="AddGitMetadaAssemblyAttributes"
          BeforeTargets="CoreGenerateAssemblyInfo">
    <ItemGroup>
        <AssemblyAttribute Include="System.Reflection.AssemblyMetadataAttribute" Condition="$(CommitHash) != ''" >
            <_Parameter1>CommitHash</_Parameter1>
            <_Parameter2>$(CommitHash)</_Parameter2>
        </AssemblyAttribute>
    </ItemGroup>
</Target> 
```

酷毙了。但是等等！并不总是管用。有时当我构建时，值不会改变。怎么回事？

所以事实证明。NET SDK 2 及更高版本中，有很多方法可以提高性能，其中之一就是出现在`obj`文件夹中的`<AssemblyName>.AssemblyInfoInputs.cache`文件，正如您所料，它只包含了一个`AssemblyInfo`输入的散列。

散列只由`_Parameter1`值在内部计算，所以在我们的例子中，我们把变化的数据放在`_Parameter2`中，这不会破坏缓存。我们可以在`CreateGeneratedAssemblyInfoInputsCacheFile` 中看到这个[的逻辑。](https://github.com/dotnet/sdk/blob/v2.1.4/src/Tasks/Microsoft.NET.Build.Tasks/build/Microsoft.NET.GenerateAssemblyInfo.targets#L98-L100)

作为一个简单的解决方法，我们可以暂时将我们的值添加到`_Parameter1`中，然后在哈希生成后移除它们。像这样:

```
<Target Name="GitMetadataAssemblyInfoCacheFileFix"
        BeforeTargets="CreateGeneratedAssemblyInfoInputsCacheFile" >
    <ItemGroup>
      <AssemblyAttribute Include="TemporaryAdditionalHashItem" Condition=" $(CommitHash) != '' ">
        <_Parameter1>$(CommitHash)</_Parameter1>
      </AssemblyAttribute>
    </ItemGroup>
</Target>
<Target Name="CleanupTemporaryAdditionalHashItem"
  AfterTargets="CreateGeneratedAssemblyInfoInputsCacheFile;AddGitMetadaAssemblyAttributes" >
    <ItemGroup>
      <AssemblyAttribute Remove="TemporaryAdditionalHashItem" />
    </ItemGroup>
</Target> 
```

现在，每当附加值发生变化时，我们都会重新编译。最终结果，[看这里](https://github.com/justeat/httpclient-interception/blob/2a7cca38244435ec4065b2030748a4a876759e2d/Directory.Build.targets#L9-L48)。

更新:在撰写本文时即将发布的 SDK(2 . 1 . 300)中，不再需要这个 hack，因为@natemcmaster [已经在这里修复了它](https://github.com/dotnet/sdk/commit/73fb22a07091585380ead0bcbcdc38839a0266e8#diff-5b286bd8dee67543f08738e5605f0d3fR99)。万岁！