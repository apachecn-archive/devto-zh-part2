# 从 MEF1 迁移到 MEF2

> 原文：<https://dev.to/jeikabu/migrating-from-mef1-to-mef2-o87>

*注意:我最初在 2017/12/28 写了这篇文章的大部分，现在我把它贴出来，因为它在当时是有用的。*

同时将我们的部分代码库转移到[。Net Standard 2.0，所以一切都在上面运行。Net Core](https://msdn.microsoft.com/en-us/magazine/mt842506.aspx) ，我们不得不处理从 [MEF1](https://docs.microsoft.com/en-us/dotnet/framework/mef/) (不在。Net Core)到 [MEF2](https://www.nuget.org/packages/microsoft.composition) (也就是)。

这篇博文让我开始行动，或者至少证实了这一举动是可能的。但是，它没有真正符合我们用途的细节。

## 套餐

最简单的改变就是从。NetFramework:

```
using System.ComponentModel.Composition;
using System.ComponentModel.Composition.Hosting; 
```

Enter fullscreen mode Exit fullscreen mode

至 nuget 提供的软件包:

```
using System.Composition;
using System.Composition.Hosting; 
```

Enter fullscreen mode Exit fullscreen mode

值得一提的是`System.ComponentModel.DataAnnotations`可以原样使用，但是包还是要从 nuget 下载。

## 代码修改

似乎其他几个人也在处理类似的问题，因为有几个 StackOverflow 问题很有帮助:

*   [https://stack overflow . com/questions/41784393/how-to-emit-a-type-in-net-core](https://stackoverflow.com/questions/41784393/how-to-emit-a-type-in-net-core)
*   [https://stack overflow . com/questions/24935261/me F2-how-to-get-metadata-from-composition host-export-with-conventi on-based-import](https://stackoverflow.com/questions/24935261/mef2-how-to-get-metadata-from-compositionhost-export-with-convention-based-impor)
*   [https://stack overflow . com/questions/13914256/strong-typed-metadata-in-me F2-system-composition](https://stackoverflow.com/questions/13914256/strongly-typed-metadata-in-mef2-system-composition)
*   [https://stack overflow . com/questions/10988447/mef-getexportst-tmetadataview-returning-nothing-with-allow multiple-true](https://stackoverflow.com/questions/10988447/mef-getexportst-tmetadataview-returning-nothing-with-allowmultiple-true)

我们有一个动态的

```
AssemblyBuilder assembly = AppDomain.CurrentDomain.DefineDynamicAssembly(name, AssemblyBuilderAccess.Run);  
// SNIP
Type result = tb.CreateType(); 
```

Enter fullscreen mode Exit fullscreen mode

需要更改为:

```
AssemblyBuilder assembly = AssemblyBuilder.DefineDynamicAssembly(name, AssemblyBuilderAccess.Run);  
// SNIP
Type result = tb.CreateTypeInfo(); 
```

Enter fullscreen mode Exit fullscreen mode

更复杂的情况是我们如何加载程序集，首先通过 MEF1:

```
CompositionContainer container;  
[ImportMany(typeof(IWorker))]  
IEnumerable<Lazy<IWorker, IWorkerMetadata>> workerPlugins { get; set; }  
public void Init(string path)  
{  
  var files = Directory.EnumerateFiles(path, "*Service.dll", SearchOption.AllDirectories);  
  var catalog = new AggregateCatalog();  
  foreach (var file in files)  
  {  
    try  
    {  
      var asmCat = new AssemblyCatalog(file);  
      if (asmCat.Parts.ToList().Count > 0)  
        catalog.Catalogs.Add(asmCat);  
    }  
    catch (ReflectionTypeLoadException)  
    {  
    }  
    catch (BadImageFormatException)  
    {  
    }  
  }  
  container = new CompositionContainer(catalog);  
  container.ComposeParts(this);  
} 
```

Enter fullscreen mode Exit fullscreen mode

然后用 MEF2:

```
CompositionHost container;  
public void Init(string path)  
{  
  var configuration = new ContainerConfiguration();  
  var files = Directory.EnumerateFiles(path, "*Service.dll", SearchOption.AllDirectories);  
  foreach (var file in files)  
  {  
    try  
    {  
       var asm = Assembly.LoadFrom(file);  
       configuration.WithAssembly(asm);  
    }  
    catch (ReflectionTypeLoadException)  
    {  
    }  
    catch (BadImageFormatException)  
    {  
    }  
  }  
  container = configuration.CreateContainer();  
} 
```

Enter fullscreen mode Exit fullscreen mode

这两者都被简化了，但是仍然应该是说明性的。

## 并排

在最初的帖子中，我在这里描述了我们如何在同一个应用程序中加载 MEF1 和 MEF2 程序集。

我以为 MEF1 是和。Net Framework 程序集和 MEF2 与。净标准。任何与……Net 标准必须放在框架程序集中，因此 MEF1。我意识到 MEF2 可以加载针对任一框架的程序集，并删除了我们所有的 MEF1 代码。

在任何情况下，您都可以在应用程序中加载这两者。如果您有许多现有的/遗留的模块，这可能是有用的。

## 待办事宜

我积压了一段时间的任务之一是重新审视我们如何处理 MEF2。特别是:

*   验证我们签名的程序集
*   加载/初始化前检查元数据

当我这样做时，肯定会有后续文章。