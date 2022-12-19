# 更新新奥尔良项目，为新奥尔良范例做好准备！

> 原文：<https://dev.to/kritner/updating-orleans-project-to-be-more-ready-for-new-orleans-examples-41n2>

[![](../Images/9f56805b1e80a60596a0c264454272c7.png)T2】](https://cdn-images-1.medium.com/max/1024/1*cP_6oahXjBJX4cwiohTMjQ.png)

请注意，这不是一篇奥尔良的帖子，不完全是——它只是我想在继续演示更多奥尔良功能之前，在我的奥尔良项目上增强的一些东西！(还有，我需要学习更多的奥尔良特色来演示！)

在继续我的 Microsoft Orleans 系列之前，我想让控制台应用程序调用我的 Orleans Grains 在各种示例下使用起来更简单一些。我该怎么做呢？多态性来拯救！

在之前的几篇 Orleans 帖子(底部的链接)中，我在我的控制台应用程序的 Program.Main 中交换了对各种 Orleans 方法的进出调用。

看起来像这样:

```
using (var client = await StartClientWithRetries())
{
 //await DoClientWork(client);
 await DoStatefulWork(client);
 Console.ReadKey();
} 
```

在上文中，当我添加新奥尔良功能时，我会简单地添加额外的调用和/或注释掉以前使用的功能。我不确定我会用这些奥尔良的例子走多远，但是因为这是我的第三个或第四个例子，我想要一个更好的方法来管理这些电话。此外，让控制台应用程序的用户可以选择使用哪个函数，而不必要求他们在运行之间更改代码。

我们如何做到这一点？通过几个简单的步骤:

*   向控制台应用程序添加一个“菜单”系统
*   让菜单系统内容包含已经实现的 Orleans 功能(到目前为止我已经展示了不同的 Orleans 特性)。

### 多态性

[![](../Images/87bb9ae6c2f4fd709f6a8fe88c42bb12.png)](https://cdn-images-1.medium.com/max/870/0*xx-wMUksAnr6sNa1) 

<figcaption>这是一个变形的[魔兽世界](https://worldofwarcraft.com/en-us/)玩家！</figcaption>

我们以前接触过多态性，尽管可能没有用它的名字来称呼它。来自[维基百科](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)#Ad_hoc_polymorphism):

> 在[编程语言](https://en.wikipedia.org/wiki/Programming_language)和[类型理论](https://en.wikipedia.org/wiki/Type_theory)中，**多态**是为不同[类型](https://en.wikipedia.org/wiki/Data_type)[【1】](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)#cite_note-1)的实体提供一个单一的[接口](https://en.wikipedia.org/wiki/Interface_(computing))或者使用一个单一的符号来表示多个不同的类型。

这是什么意思？基本上——使用接口和/或其他抽象(比如抽象类)。它如何与我试图完成的目标相适应？我正在实现一系列不同的例子，这些例子调用一个奥尔良集群来展示奥尔良的一个特征。那么我可以使用什么样的接口定义呢？我们先来看看:

```
public interface IOrleansFunctions
{
 Task PerformFunction();
} 
```

在上面的例子中，我们有一个只做`PerformFunction`(不管它最终意味着什么)并返回一个任务的方法。

嗯，另一件需要用到的事情是使用一个`IClusterClient`。我有几种方法可以做到这一点，将它从抽象中分离出来，并使它对实现类可用，或者只是将它作为接口方法的一个参数。在后面我将使用实现的类的方式中，我选择将`IClusterClient`作为接口方法的一个参数。更新后看起来像:

```
public interface IOrleansFunctions
{
 Task PerformFunction(IClusterClient clusterClient);
} 
```

我们需要的最后一件事(至少现在)是描述`IOrleansFunction`的某种方法。我们可以通过这个简单的加法做到这一点:

```
public interface IOrleansFunction
{
 string Description { get; }
 Task PerformFunction(IClusterClient clusterClient);
} 
```

现在，我们可以为每个实现提供一个描述—我们将使用它作为控制台应用程序菜单中的“选项”。

那么为什么要经历这么多麻烦呢？在走这条路之前，我很难实现新的功能，不需要“移动一堆东西”和持续编辑已经存在的类。我现在该如何避免这种情况？嗯，我现在需要做的唯一一件事(理想情况下，如果我做得对的话)是添加一个新的`IOrleansFunction`实现，系统会毫不费力地选择它。

为什么多态是整洁的？因为您可以与接口方法进行交互，而无需关心实际的实现。这使得你的代码更加[松散耦合](https://en.wikipedia.org/wiki/Loose_coupling)，并且像单元测试和代码可维护性这样的事情更加简单；这是我在其他地方写的一些文章中提到的(底部的链接)。

#### 实现了

我做过几个独立的奥尔良例子:

*   你好世界
*   多重实例化
*   有状态颗粒

这些对于新的实现来说似乎是完美的！

这将主要是从以前的帖子复制/粘贴，只是到他们的新的抽象，并提供一个描述。

HelloWorld:

```
public class HelloWorld : IOrleansFunction
{
 public string Description => "Demonstrates the most basic Orleans function of 'Hello World'.";

 public async Task PerformFunction(IClusterClient clusterClient)
 {
  var grain = clusterClient.GetGrain<IHelloWorld>(Guid.NewGuid());
  Console.WriteLine("Hello! What should I call you?");
  var name = Console.ReadLine();

  Console.WriteLine(await grain.SayHello(name));

  ConsoleHelpers.ReturnToMenu();
 }
} 
```

多重实例化:

```
public class MultipleInstantiations : IOrleansFunction
{
 public string Description => "Demonstrates multiple instances of the same grain.";

 public async Task PerformFunction(IClusterClient clusterClient)
 {
  var grain = clusterClient.GetGrain<IHelloWorld>(Guid.NewGuid());
  var grain2 = clusterClient.GetGrain<IHelloWorld>(Guid.NewGuid());

  Console.WriteLine($"{await grain.SayHello("1")}");
  Console.WriteLine($"{await grain2.SayHello("2")}");
  Console.WriteLine($"{await grain.SayHello("3")}");

  ConsoleHelpers.ReturnToMenu();
 }
} 
```

有意义的工作:

```
public class StatefulWork : IOrleansFunction
{
 public string Description => "Demonstrates using stateful grains with numerous instantiations.";

 public async Task PerformFunction(IClusterClient clusterClient)
 {
  var kritnerGrain = clusterClient.GetGrain<IVisitTracker>("[kritner@gmail.com](mailto:kritner@gmail.com)");
  var notKritnerGrain = clusterClient.GetGrain<IVisitTracker>("[notKritner@gmail.com](mailto:notKritner@gmail.com)");

  await PrettyPrintGrainVisits(kritnerGrain);
  await PrettyPrintGrainVisits(notKritnerGrain);

  ConsoleHelpers.LineSeparator();
  Console.WriteLine("Ayyy some people are visiting!");

  await kritnerGrain.Visit();
  await kritnerGrain.Visit();
  await notKritnerGrain.Visit();

  ConsoleHelpers.LineSeparator();

  await PrettyPrintGrainVisits(kritnerGrain);
  await PrettyPrintGrainVisits(notKritnerGrain);

  ConsoleHelpers.LineSeparator();
  Console.Write("ayyy kritner's visiting even more!");

  for (int i = 0; i < 5; i++)
  {
   await kritnerGrain.Visit();
  }

  ConsoleHelpers.LineSeparator();

  await PrettyPrintGrainVisits(kritnerGrain);
  await PrettyPrintGrainVisits(notKritnerGrain);

  ConsoleHelpers.ReturnToMenu();
 }

 private static async Task PrettyPrintGrainVisits(IVisitTracker grain)
 {
  Console.WriteLine($"{grain.GetPrimaryKeyString()} has visited {await grain.GetNumberOfVisits()} times");
 }
} 
```

### 菜单

现在我们已经完成了`IOrleansFunction`的实现，我们只需要将它们插入到我们将要创建的菜单系统中！

使用菜单系统，用户应该能够输入对应于特定奥尔良特征的数字，然后该特征应该执行。

菜单上需要一些东西:

*   要列出的一系列奥尔良特色
*   一种显示特征的方法
*   一种执行功能的方式
*   用户尝试多种功能并退出菜单的方式

#### 一组新奥尔良特色列表

我们需要保存一个我们的`OrleansFeatures`的集合，还有什么比使用另一个接口更好的方法呢！我已经将新的接口`IOrleansFunctionProvider`定义为:

```
public interface IOrleansFunctionProvider
{
 IList<IOrleansFunction> GetOrleansFunctions();
} 
```

上面的接口除了在被调用时返回一个`IList<IOrleansFunctions>`之外什么也不做。

所述接口的具体化也足够简单:

```
public class OrleansFunctionProvider : IOrleansFunctionProvider
{
 public IList<IOrleansFunction> GetOrleansFunctions()
 {
  return new List<IOrleansFunction>()
  {
   new HelloWorld(),
   new MultipleInstantiations(),
   new StatefulWork()
  };
 }
} 
```

在上面的例子中，我们只是更新并返回我们之前创建的当前`OrleansFunctions`的列表。

#### “一种显示特征的方式”和“一种执行特征的方式”

现在我们需要一种在菜单上显示功能的方法——幸运的是，我们提前考虑了，并在我们的`IOrleansFeature`界面上创建了一个描述。

再加上我们的`IOrleansFeatureProvider`，意味着我们可以枚举提供者返回给我们的内容，打印出每个描述。我们将使用一个[稍微有点粗糙(？)]通过集合的索引将一个特性分配给一个数字的方法，但是/耸肩，没关系吧？

让我们开始一个新的课程:

```
public class OrleansExamples
{
 private readonly IOrleansFunctionProvider _orleansFunctionProvider;

 public OrleansExamples(IOrleansFunctionProvider orleansFunctionProvider)
 {
  _orleansFunctionProvider = orleansFunctionProvider;
 }

 public async Task ChooseFunction(IClusterClient clusterClient)
 {
  var orleansFunctions = _orleansFunctionProvider.GetOrleansFunctions();

  var input = string.Empty;
  while (true)
  {
   Console.WriteLine("Pick a function to use for Orleans demonstration:");
   ConsoleHelpers.LineSeparator();
   for (int i = 0; i < orleansFunctions.Count; i++)
   {
    Console.WriteLine($" {i} - {orleansFunctions[i].Description}");
   }

   ConsoleHelpers.LineSeparator();
   input = Console.ReadLine();

   if (!int.TryParse(input, out var inputResult))
   {
    Console.WriteLine("Invalid Input. Please input a number.");
    continue;
   }

   try
   {
    await orleansFunctions[inputResult].PerformFunction(clusterClient);
    ConsoleHelpers.LineSeparator();
   }
   catch(ArgumentOutOfRangeException)
   {
    Console.WriteLine("Invalid Input. Please ensure you pick a number/function from the provided list.");
    ConsoleHelpers.LineSeparator();
   }
  }

  return;
 }
} 
```

在上面的例子中，我们使用由我们的`IOrleansFunctionProvider`提供的结果，枚举它们，打印出`IList<IOrleansFunction>`索引和`IOrleansFunction.Description`，解析用户输入，并尝试根据索引调用集合上的适当方法。请注意，我们在这里只处理与`IOrleansFunctionProvider`和`IOrleansFunction`相关的接口。这个类不知道实现者是谁，因为对于这个类的范围(松散耦合)来说，这并不重要。

#### 供用户尝试多种功能，并从菜单中退出

这是一个简单的事情，添加一个 if 条件来检查退出菜单的特定条目，因为目前，我们的菜单将无限循环。

向类中添加新的 const:

```
private const string ESCAPE_STRING = "-1"; 
```

以及 while 循环中的一个新条件，用于在执行 grain 示例之前检查转义字符串:

```
if (input == ESCAPE_STRING)
{
 Console.WriteLine("Exiting...");
 return;
} 
```

### 把所有的东西放在一起

完成我们菜单的最后一部分，是将新的比特插入程序。应用程序的 Main。

这很容易做到，因为主要是*删除代码！我喜欢删除代码！*

我们的原创:

```
private static async Task<int> RunMainAsync()
{
 try
 {
  using (var client = await StartClientWithRetries())
  {
   //await DoClientWork(client);
   await DoStatefulWork(client);
   Console.ReadKey();
  }

  return 0;
 }
 catch (Exception e)
 {
  Console.WriteLine(e);
  Console.ReadKey();
  return 1;
 }
}

private static async Task DoClientWork(IClusterClient client)
{
 // example of calling grains from the initialized client
 var grain = client.GetGrain<IHelloWorld>(Guid.NewGuid());
 var grain2 = client.GetGrain<IHelloWorld>(Guid.NewGuid());
 Console.WriteLine($"{await grain.SayHello("1")}");
 Console.WriteLine($"{await grain2.SayHello("2")}");
 Console.WriteLine($"{await grain.SayHello("3")}");
 PrintSeparatorThing();
}

private static async Task DoStatefulWork(IClusterClient client)
{
 var kritnerGrain = client.GetGrain<IVisitTracker>("[kritner@gmail.com](mailto:kritner@gmail.com)");
 var notKritnerGrain = client.GetGrain<IVisitTracker>("[notKritner@gmail.com](mailto:notKritner@gmail.com)");
 await PrettyPrintGrainVisits(kritnerGrain);
 await PrettyPrintGrainVisits(notKritnerGrain);
 PrintSeparatorThing();
 Console.WriteLine("Ayyy some people are visiting!");
 await kritnerGrain.Visit();
 await kritnerGrain.Visit();
 await notKritnerGrain.Visit();
 PrintSeparatorThing();
 await PrettyPrintGrainVisits(kritnerGrain);
 await PrettyPrintGrainVisits(notKritnerGrain);
 PrintSeparatorThing();
 Console.Write("ayyy kritner's visiting even more!");
 for (int i = 0; i < 5; i++)
 {
  await kritnerGrain.Visit();
 }
 PrintSeparatorThing();
 await PrettyPrintGrainVisits(kritnerGrain);
 await PrettyPrintGrainVisits(notKritnerGrain);
}

private static async Task PrettyPrintGrainVisits(IVisitTracker grain)
{
 Console.WriteLine($"{grain.GetPrimaryKeyString()} has visited {await grain.GetNumberOfVisits()} times");
}

private static void PrintSeparatorThing()
{
 Console.WriteLine($"{Environment.NewLine}-----{Environment.NewLine}");
} 
```

该死…太多了。但是所有这些代码现在都变成了…

```
private static async Task<int> RunMainAsync()
{
 try
 {
  using (var client = await StartClientWithRetries())
  {
   await new OrleansExamples(new OrleansFunctionProvider())
    .ChooseFunction(client);
  }

  return 0;
 }
 catch (Exception e)
 {
  Console.WriteLine(e);
  Console.ReadKey();
  return 1;
 }
} 
```

删除了大量代码！(当然，很多代码都被重构到了个体中`IOrleansFunctions`。但是我们再也不用看那些代码了！

那么这一切看起来像什么？

应用程序启动:

[![](../Images/5b98f1dff947023ba0e25d5ccd718781.png)](https://cdn-images-1.medium.com/max/1024/1*D1OyfPOsS4ru_lZhs0GbiA.png) 

<figcaption>申请开始</figcaption>

选择“Hello World”示例(选项 0):

[![](../Images/0c19a2d151b4004fa197c6a6efc183f4.png)](https://cdn-images-1.medium.com/max/1024/1*q5tLXqaU9-JqY3R0nEnizg.png) 

<figcaption>选择 Hello World 示例</figcaption>

选择有状态颗粒的例子(选项 2):

[![](../Images/bdd583c53dc332cb9f3aa4d666130c7a.png)](https://cdn-images-1.medium.com/max/1024/1*7gcHpRQg3u-jBB1zoGgdlA.png) 

<figcaption>选择有状态的谷物例子</figcaption>

退出(选择 1):

[![](../Images/6f06b1d5dea3298beb49632b88a9265a.png)](https://cdn-images-1.medium.com/max/1024/1*S_9jcf1zlFPndl_oNhB_zw.png) 

<figcaption>退出</figcaption>

这篇文章比我预期的要长，但希望它能帮助传达使用接口如何帮助更好地抽象和分解你需要做的工作。这再加上抽象的其他“特性”,比如单元测试、松散耦合和更容易的应用程序架构“高级视图”,是我如此喜欢研究抽象的原因。

这篇文章的代码可以在 https://github . com/krit ner-Blogs/OrleansGettingStarted/releases/tag/v 0.30 中找到

相关:

*   [微软奥尔良入门](https://medium.com/@kritner/getting-started-with-microsoft-orleans-882cdac4307f?source=friends_link&sk=1fc3451d71a19dcb49f2c8bbeb6b079e)
*   [微软奥尔良——再利用谷物和谷物状态](https://medium.com/@kritner/microsoft-orleans-reusing-grains-and-grain-state-136977facd42?source=friends_link&sk=f19cfa3f17665c3d700bfe0df56e27a9)
*   发布时的 GitHub 代码—[https://GitHub . com/krit ner-Blogs/OrleansGettingStarted/releases/tag/v 0.30](https://github.com/Kritner-Blogs/OrleansGettingStarted/releases/tag/v0.30)
*   [这都是关于抽象的宝贝](https://medium.com/@kritner/its-all-about-the-abstractions-baby-964983131ef1?source=friends_link&sk=db4cb41d3a3718bfb8356262b070903b)
*   单元测试的商业价值是什么？
*   [开始单元测试和 Moq](https://medium.com/@kritner/getting-started-with-unit-testing-and-moq-part-1-aac0935310cc?source=friends_link&sk=82f5134e0939535b1a06ef02550b2a5a)