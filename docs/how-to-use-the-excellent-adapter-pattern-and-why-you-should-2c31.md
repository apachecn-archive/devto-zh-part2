# 如何使用优秀的适配器模式以及为什么应该这样做

> 原文：<https://dev.to/kylegalbraith/how-to-use-the-excellent-adapter-pattern-and-why-you-should-2c31>

适配器模式被归类为一种结构模式，它允许一段代码与另一段不直接兼容的代码对话。

首先，为了接下来的几分钟，让我们在我们负责的 web 应用程序的范围内构建我们的上下文。该应用程序是一个经典的三层应用程序，前端客户端、API 的 web 服务器和存储数据的地方。如今，这是一个非常传统的筹码。

现在要调出:*一个存放数据的地方*。这可能是 SQL Server 或 MongoDB 之类的数据库。它也可能只是一个我们转储数据的地方，比如 AWS S3，或者甚至是我们的硬盘。

如果我们的应用程序从不关心我们在哪里存储或读取数据，那就更好了。如果我们为这些操作定义了一个公共接口，我们就可以在不改变应用程序的情况下改变存储或读取数据的位置。为此，我们利用了[存储库模式](https://dev.to/kylegalbraith/getting-familiar-with-the-awesome-repository-pattern--1ao3)。

适配器模式是可以在存储库中使用的模式。这种模式允许您的应用程序代码利用一致的接口来处理另一段代码，而无需依赖该代码。在这篇文章中说得更直白一点，我们将定义一个适配器作为连接两段不连贯代码的公共接口。

跟我到目前为止？如果你不是也没关系，让我们看看你什么时候会用它来试着理清事情。

### 发现适配器模式的(潜在)需求

要发现适配器模式的潜在用途，有时可能比听起来要困难。这通常是因为大多数现代编程语言已经内置了适配器。但是让我们假设我们有一个如下定义的`IPerson`接口。

```
public interface Person : IPerson
{
    public string Name { get; set; }
    public string City { get; set; }
    public string IdNumber { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们有一段老代码，它总是负责加载`Person`对象。

```
public class PersonLoader: IPersonLoader
{
    public Person LoadPerson()
    {
        //code to load a person
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意这里的`PersonLoader`实现了一个公共接口`IPersonLoader`，它定义了一个叫做`LoadPerson()`的方法的必要性。让我们看看正在使用`PersonLoader`的代码。

```
public class LoadPeople
{
    static void Main(string[] args)
    {
        IPersonLoader loader = new PersonLoader();
        var person = loader.LoadPerson();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的加载器是类型`IPersonLoader`，而`PersonLoader`恰好实现了那个接口。因此创建了一个`PersonLoader`，然后代码可以调用`LoadPerson()`。到目前为止一切顺利，对吗？

但是请记住，`PersonLoader`是一段糟糕的代码，我们希望在不破坏整个应用程序的情况下将其删除。具体来说，我们希望使用一个新的改进的加载器开始加载`IPerson`对象。

我们这里有一个适配器模式的很好的用例。

### 在适配器中添加 spice

首先，我们定义了新的和改进的 person loader，`BetterPersonLoader`，它实现了包含新方法`RunGetPerson()`的`IBetterPersonLoader`。

```
public class BetterPersonLoader : IBetterPersonLoader
{
    public Person RunGetPerson()
    {
        //code to get person the new and improved way.
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是我们不能仅仅将`BetterPersonLoader`插入到我们的`LoadPeople`客户端。它不兼容，会破坏代码，因为代码需要一个`IPersonLoader`接口。

所以我们能做的就是定义一个`PersonAdapter`来实现这个接口。让我们看看那会是什么样子。

```
public class PersonAdapter : IPersonLoader
{   
    public Person LoadPerson()
    {
        var newLoader = new BetterPersonLoader();
        return newLoader.RunGetPerson();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以更新我们的`LoadPeople`客户端来利用我们的新适配器。

```
public class LoadPeople
{
    static void Main(string[] args)
    {
        IPersonLoader loader = new PersonAdapter();
        var person = loader.LoadPerson();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，这段代码在将来也能适应这种变化。如果`BestPersonLoader`出现，我们可以更新我们的`PersonAdapter`类，并且`LoadPeople`客户端**永远不需要改变**。

```
public class PersonAdapter : IPersonLoader
{   
    public Person LoadPerson()
    {
        var newLoader = new BestPersonLoader();
        return newLoader.BestWayToGetPerson();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 利弊

适配器模式有许多不同的风格。您可以在从某个地方加载数据这样简单的事情中看到它。或者您可以在更复杂的实现中看到它，如 HTTP 客户端。适配器模式引入了许多好处:

*   它增加了可重用性和灵活性。适配器的接口已定义并达成一致。因此，只要协议得到维护，我们就可以在适配器中更改实现。
*   客户端变得简单。正如我们在示例中看到的，在重构为适配器模式之后，我们现在可以更改为我们想要的任何类型的加载器逻辑。客户端不必做出这个决定，因为适配器已经同意了`IPersonLoader`契约。
*   改变和尝试新的想法，打破和改变更少的东西。耦合被最小化到仅仅是商定的契约，该契约的实现是自由解释的。

这些都是恒星的好处，但我会回忆，如果我没有警告你潜在的陷阱，你可能会落入。

*   兔子踪迹效应。如果您有深度嵌套的对象，有时适配器可能会发挥到极致。你可以结束`AdapterA`呼叫`AdapterB`呼叫`AdapterC`只是为了装载一个人。
*   容易过度工程化。注意，在这篇文章中，我从一段代码开始，**没有使用**适配器模式。这是因为这是一种通常**出现**的模式，并且从一开始就不为人所知。当您实际上不需要适配器模式时，尽量不要尝试和引入适配器模式。

### 饿着肚子学亚马逊 Web 服务？

有很多人渴望学习亚马逊网络服务。受到这个事实的启发，我创建了一个课程，专注于通过使用它来学习 Amazon Web Services。关注静态网站的托管、保护和交付问题。通过构建问题的解决方案，您可以学习 S3、API Gateway、CloudFront、Lambda 和 WAF 等服务。

AWS 周围有大量的信息。很容易迷失方向，在学习上没有任何进步。通过解决这个问题，我们可以简化信息，加快你的学习。我用这本书和视频课程的目的是与你分享我所学到的。

听起来有趣吗？查看登录页面以了解更多信息，并选择一个适合您的软件包，[通过实际使用它来学习 AWS 基础知识](https://www.kylegalbraith.com/learn-aws/)。