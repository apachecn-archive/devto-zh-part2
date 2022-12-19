# 开始使用 Microsoft Orleans

> 原文：<https://dev.to/kritner/getting-started-with-microsoft-orleans-1765>

Orleans 是微软研究院构建的一个[开源](https://github.com/dotnet/orleans) actor 框架，用于[光环云功能](https://channel9.msdn.com/Events/Build/2014/3-641)！什么是演员框架？另一个流行。net actor framework 是 AKKA.net 的，尽管我没有使用过它——也没有接触过 Orleans。无论如何…

来自维基百科:

> [计算机科学](https://en.wikipedia.org/wiki/Computer_science)中的 **actor 模型**是[并发计算](https://en.wikipedia.org/wiki/Concurrent_computation)的[数学模型](https://en.wikipedia.org/wiki/Mathematical_model)，将“actor”作为并发计算的通用原语。为了响应收到的[消息](https://en.wikipedia.org/wiki/Message_(computing))，参与者可以:做出本地决策、创建更多参与者、发送更多消息，以及确定如何响应收到的下一条消息。参与者可以修改他们自己的[私有状态](https://en.wikipedia.org/wiki/Private_state)，但是只能通过消息相互影响(避免需要任何[锁](https://en.wikipedia.org/wiki/Lock_(computer_science)))。

#### 我为什么要关心奥尔良，或者一般的演员模型框架？

在整体式系统中，您或多或少只能“纵向扩展”。对于使用微服务、actors 等构建的系统，您可以选择向外扩展。“向上扩展”与“向外扩展”意味着什么？向上扩展系统意味着向运行系统的硬件添加更多的 RAM、更多的 CPU——更多的资源；但是你仍然被限制在一个“盒子”里。向外扩展意味着您可以添加一台全新的机器，通常是添加到某种“集群”中，这使得您的系统可以更轻松地添加额外的资源。当然，您可以在微服务系统中向现有机器添加更多 RAM/CPU，但是您也可以选择拥有更多机器！选择总是美好的！

这一切意味着什么？随着当今世界上所有云服务、容器化和虚拟机的出现，根据需要增加和减少资源会变得非常简单。只需向群集中添加一个新节点！

#### 奥尔良能做什么？

请注意，所有这些信息，以及更详细的信息，都可以在 [Orleans 文档中找到。](https://dotnet.github.io/orleans/Documentation/index.html)奥尔良的作品有几个概念:

*   颗粒——在上面的角色模型定义中描述的“虚拟角色”和/或“原语”。颗粒是实际上包含你要分发的逻辑的对象。每个单独的颗粒保证在单线程执行模型中运行，从而极大地简化了编程，并避免了竞争情况。这些颗粒是以异步方式编写的，旨在用于非常快速的运行操作(< 200 毫秒 IIRC)——尽管我将它用于需要更长时间的操作，但如果一切正常的话，也许我可以在某个时候发布一篇关于它的帖子！
*   筒仓——存放你的“谷物”的地方。一个筒仓可以包含许多颗粒类型，以及这些类型的许多实例，这取决于您的需要。
*   集群—一组孤岛。这允许奥尔良的“向外扩展”部分。如果需要更多或更少的资源，您可以简单地注册或删除集群上的孤岛。轻松扩展！

#### 奥尔良应用示例

我希望随着我了解的越来越多，我可以把一些更多的功能应用程序放在一起，但这只是开始…

一个 Orleans 应用程序由几个独立的部分组成，通常都是独立的项目:

*   颗粒界面
*   粒度实现
*   奥尔良筒仓主机
*   奥尔良客户

我们开始吧！

*   确保你有[。安装了 net core sdk](https://www.microsoft.com/net/download) 以及一个选择的 IDE 比如 [Visual Studio 或者 Visual Studio Code](https://visualstudio.microsoft.com/downloads/)
*   运行几个命令来启动我们的项目:

```
dotnet new console -n Kritner.OrleansGettingStarted.Client
dotnet new console -n Kritner.OrleansGettingStarted.SiloHost
dotnet new classlib -n Kritner.OrleansGettingStarted.GrainInterfaces
dotnet new classlib -n Kritner.OrleansGettingStarted.Grains 
```

*   通过管理器、控制台或 csproj 文件添加 NuGet 包，看起来像…

颗粒界面/颗粒 csproj:

```
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.Orleans.Core.Abstractions" Version="2.1.0" />
    <PackageReference Include="Microsoft.Orleans.OrleansCodeGenerator.Build" Version="2.1.0" />
  </ItemGroup>

</Project> 
```

客户 csproj:

```
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
    <PackageReference Include="Microsoft.Orleans.Client" Version="2.1.0" />
  </ItemGroup>
</Project> 
```

服务器 csproj:

```
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
    <PackageReference Include="Microsoft.Orleans.Server" Version="2.1.0" />
  </ItemGroup>
</Project> 
```

*   添加奥尔良项目之间的项目相关性。Grains、客户机和服务器都应该依赖于 GrainsInterfaces 项目。SiloHost 项目还应依赖于 Grains 项目。看起来应该是这样的:

[![](../Images/dadb6d6be12cb36b83c4e1b701e6d893.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--akcAW-an--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/705/1%2AolhVjOfpfKiPzFKuHx7TPA.png)

以上就是《新奥尔良入门》需要的全部内容！此时的回购虽然很少，但可以在以下位置找到:[https://github . com/krit ner-Blogs/OrleansGettingStarted/tree/8944333 AE 23 f 21 e 7873 a 356 a 191 cece B3 cc 91 c 97](https://github.com/Kritner-Blogs/OrleansGettingStarted/tree/8944333ae23f21e7873a356a191ceceb3cc91c97)

请注意，在上面链接的图像和回购中，我在这一点上遗漏了一个依赖项。SiloHost 项目还应提及 Grains 项目，这不会在上述时间点反映出来。您也可以继续添加对 Microsoft 的引用。客户端/silo host 中的 Extensions.Logging.Console(稍后将需要)。

#### 那个愚蠢的你好世界奥尔良的例子

先说最基础的例子——hello world。这不会真正展示奥尔良能做得很好，但我们必须从某个地方开始，对吗？

让我们在我们的 GrainInterfaces 项目中引入一个 IHelloWorld 接口:

```
namespace Kritner.OrleansGettingStarted.GrainInterfaces
{
    public interface IHelloWorld : IGrainWithGuidKey
    {
        Task<string> SayHello(string name);
    }
} 
```

几个(可能？)非标准的事情发生在你可能习以为常的事情上面:

*   IHelloWorld 实现了 IGrainWithGuidKey——一个定义奥尔良纹理及其键类型的接口。我相信所有的键类型最终都会被转换成 Guid，所以这是我通常坚持使用的方法，除非有一些独特的上下文数据可以用于颗粒识别。
*   Task-所有的 Orleans grains 应该以一种[异步方式](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/)被编程，这样，所有的 grain 将在一个最小任务(void)返回，如果不是一个任务(一个返回值)。

上面的 grain 接口只是接受一个字符串名并返回一个任务。

现在在 Grains 项目中实现 HelloWorld 类:

```
namespace Kritner.OrleansGettingStarted.Grains
{
    public class HelloWorld : Grain, IHelloWorld
    {
        public Task<string> SayHello(string name)
        {
            return Task.FromResult($"Hello World! Orleans is neato torpedo, eh {name}?");
        }
    }
} 
```

同样，这里大部分是标准的东西。我们正在扩展一个基本的谷物类。从我们的 IHelloWorld 实现，并提供实现。我们的方法没什么特别的，所以没有理由等待结果(如果我错了，有人能纠正我吗？Async/await 对我来说还很陌生)。

现在，除了整个客户机/服务器设置和配置之外，我们已经具备了 Orleans 工作所必需的一切——接下来！

可以找到帖子中截至此点的工作报告:[https://github . com/krit ner-Blogs/OrleansGettingStarted/tree/d 244 f 6 e 67384 d8e 992 e 15625 f 619072863429663](https://github.com/Kritner-Blogs/OrleansGettingStarted/tree/d244f6e67384d8e992e15625f619072863429663)

#### 客户端/服务器设置

接下来是客户机和服务器的设置，我们将在目前尚未涉及的客户机和 SiloHost 项目中进行。

请注意，下面的配置专门用于开发，它不能像生产配置那样作为节点群集(AFAIK)运行。

客户。Program.cs(注意或多或少抄袭自([https://github . com/dot net/Orleans/blob/master/Samples/2.0/hello world/src/Orleans client/program . cs](https://github.com/dotnet/orleans/blob/master/Samples/2.0/HelloWorld/src/OrleansClient/Program.cs)):