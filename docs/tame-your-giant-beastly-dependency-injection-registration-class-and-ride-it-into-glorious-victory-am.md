# 驯服你的巨大的，野兽般的依赖注入注册类，并乘坐它进入辉煌的胜利

> 原文：<https://dev.to/scotthannen/tame-your-giant-beastly-dependency-injection-registration-class-and-ride-it-into-glorious-victory-am>

我们使用依赖注入容器(dependency injection containers，也称为*反转控制容器*或 *IoC 容器*)来帮助我们编写更干净、更易管理的代码。那么，具有讽刺意味的是，我们代码中最混乱和无序的区域之一往往是我们向容器注册依赖关系的部分。

(有多个术语来描述向容器注册依赖项，包括*容器配置*、*组件注册*，可能还有更多。为了保持一致，我称之为*注册依赖关系*或*依赖关系注册*。)

例如，使用 Unity，注册可能看起来有点像这样，除了想象滚动页面和页面:

```
public void RegisterDependencies(IUnityContainer container)
{
    // +100 lines
    container.RegisterType<ISomeInterface, Something>("This");
    container.RegisterType<ISomeInterface, SomethingElse>("That", 
        new InjectionProperty("connectionString",
        ConfigurationManager.ConnectionStrings["thatConnectionString"].ConnectionString));
    container.RegisterType<IGoesNorth, Train>();
    container.RegisterType<Boat>();
    container.RegisterInstance<IGoesSouth>(container.Resolve<Boat>());
    // + 200 more lines
} 
```

Enter fullscreen mode Exit fullscreen mode

…除了你可以在五个屏幕中滚动浏览之外，它没有可辨别的顺序，并且它注册了你的应用中使用的三个或四个不同库的依赖关系，所有这些都混合在一起。

这并不是世界末日，因为至少通过把这些都塞进这里，我们可以让剩下的代码更整洁一些。但是这仍然会导致一些问题:

*   很难阅读和维护。
*   如果我们维护依赖于相同库的其他应用程序，我们将复制代码。除了语法上的一些变化(因为总是有不止一种方法来做同样的事情)，一些不同的名称，以及注册的顺序不同，这些重复看起来几乎是一样的。
*   尽管错误会导致致命的运行时异常，但这段代码没有测试。
*   它迫使应用程序知道太多它所依赖的类的内部工作方式。应用程序应该处理类库的接口，而不必知道它的实现细节。如果我们的应用程序必须包含注册一个类库的所有依赖项的代码，那么这个意图就落空了。
*   查找和删除未使用的注册更加困难，所以我们最终会留下我们不再需要的东西。

我们如何解决这个问题？

## 把大方法分解成小方法

如果我们有一个有 100 个注册的大方法，并且我们正在寻找一个简单的修复方法，我们可以把这个方法分解成更小的方法。首先，我们可以根据功能领域将它们分开。那么我们的主方法可能看起来更像这样:

```
RegisterUserComponents(container);
RegisterProductComponents(container);
RegisterLoggingComponents(container); 
```

Enter fullscreen mode Exit fullscreen mode

如果这是我们所能做到的，那么它仍然比一个大方法更容易管理。

## 将依赖关系注册分离成模块

大多数流行的 DI 库都提供了我们可以实现的接口或基类，这些接口或基类提供了一种熟悉的、一致的方式来向容器添加注册。

```
container.AddFacility<YourFacility>(); // Castle Windsor Facility
containerBuilder.RegisterModule(new YourModule()); // Autofac Module
container.AddNewExtension<YourContainerExtension>(); // Unity Container Extension 
```

Enter fullscreen mode Exit fullscreen mode

即使在这三个框架中，也有更多的选择。Windsor 也有*安装程序*，类似于工具，但是也有方法在一个程序集内发现和应用安装程序。关键是你有选择。(我使用 Windsor 的次数比任何其他容器都多，但是在编写或测试想法时，我喜欢使用其他容器，以确保我的设计不会与特定容器的使用相耦合。)

假设您有一个由几个类组成的 Foo 计算器，并且您希望将它的依赖项的注册清楚地分离到它自己的类中。您可以创建这样一个类:

```
public class FooCalculatorContainerExtensions : UnityContainerExtension
{
    protected override void Initialize()
    {
        Container.RegisterType<IFooCalculator, FooCalculator>();
        Container.RegisterType<IFooRepository, SqlFooRepository>();
        Container.RegisterType<IFooValidator, FooInputValidator>("FooInputValidator");
        Container.RegisterType<IFooValidator, FooDataValidator>("FooDataValidator");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，在你的应用程序的组合根中，你注册依赖于你的容器，你应该这样做:

```
container.AddNewExtension<FooCalculatorContainerExtension>(); 
```

Enter fullscreen mode Exit fullscreen mode

这难道不比那些相同的组件注册隐藏在几百行看起来相似的代码中更容易阅读吗？也许我们甚至可以从一开始就阻止这个庞大的注册类变得如此庞大。

## 有什么好处？

这个帖子的标题是否夸大了奖励？也许这实际上并不光荣……不，我要去袖手旁观，一字不差。我们不只是要通过移动代码来消除一个巨大方法的丑陋，尽管这也很棒。一旦我们驯服了这只野兽，它还有其他的方法来为我们服务。

### 复用性

如果我们正在构建一个我们或其他人将在不同项目中使用的类库，这一点尤其正确。我们库的用户不需要知道如何注册它的依赖项或者将代码添加到他们自己的容器设置中。他们很可能只用一行代码就能做到。

如果我们想让我们的库容器不可知——也就是说，它不依赖于某个特定品牌的 DI 容器——我们可以将特定于 Unity 或特定于 Windsor 的代码放在一个单独的项目中。我有时会，但这取决于具体情况，我对此并不执着。

### 可测性

假设`FooCalculator`是根对象，并且它依赖于其他类，您可以像这样编写一个单元测试:

```
[TestMethod]
public void VerifyFooCalculatorRegistration()
{
    using (var container = new Unity.UnityContainer())
    {
        container.AddNewExtension<FooCalculatorContainerExtension>();
        var resolved = container.Resolve<IFooCalculator>();
        Assert.IsNotNull(resolved);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以调用`container.Registrations`来获取所有已注册的类型，并验证您可以解析所有这些类型。

### 更简单、更完整的集成测试

如果您正在编写测试几个类的组合的集成测试，您甚至可以使用容器来简化您的测试设置。

例如:

```
[TestClass]
public class FooCalculatorIntegrationTest
{
    private IUnityContainer _container;

    [TestInitialize]
    public void Setup()
    {
        _container = new Unity.UnityContainer();
        _container.AddNewExtension<FooCalculatorContainerExtension>();
    }

    [TestMethod]
    public void TestSomethingWithMockedRepository()
    {
        var repository = new Mock<IFooRepository>();
        repository.Setup(x => x.GetFoo(It.IsAny<Guid>())).Returns(new Foo(someParticularFooDetails));

        // Replace the previous registration of the repository with the mock.
        _container.RegisterInstance<IFooRepository>(repository.Object);

        var subject = _container.Resolve<IFooRepository>();
        // Do something with the subject. Act and assert.
    }

    [TestCleanup]
    public void Cleanup()
    {
        _container.Dispose();
    }        
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们要测试有很多嵌套依赖的类，这尤其有用。如果我们已经编写了可重用的代码来设置一个容器来创建类实例，为什么不使用它呢？而且，既然这是一个集成测试，难道它不应该也测试我们用来注册依赖项的代码吗？

## 将依赖注入到我们的依赖注册类中

这听起来可能有点复杂。但是假设一个应用程序依赖于我们的 Foo 计算器库，并且通过方便地将我们的`FooCalculatorContainerExtension`添加到它的容器中来注册它的依赖项。只有一个问题:`SqlFooRepository`需要一个 SQL 连接字符串。该库无法知道那个连接字符串是什么。我们如何向`SqlFooRepository`提供这种依赖性？(出于演示的目的，我只关心一个 SQL 连接字符串。实际上，这可能是一个包含几个设置的类，也可能是某个其他类的实例。)

有几种方法。不管我们使用哪一个，关键是依赖关系是显式的。如果我们能够要求实现它以便编译代码，那是最好的。退而求其次是一个清晰、有用的运行时异常。

一个是构造函数注入到我们的容器扩展/模块/设施:

```
public class FooCalculatorContainerExtension : UnityContainerExtension
{
    private readonly string _fooRepositoryConnectionString;

    public FooCalculatorContainerExtension(string fooRepositoryConnectionString)
    {
        if(string.IsNullOrEmpty(fooRepositoryConnectionString))
            throw new ArgumentException($"{nameof(fooRepositoryConnectionString)} is required.");
        _fooRepositoryConnectionString = fooRepositoryConnectionString;
    }

    protected override void Initialize()
    {
        Container.RegisterType<IFooCalculator, FooCalculator>();
        Container.RegisterType<IFooRepository, SqlFooRepository>
            (new InjectionProperty("connectionString", _fooRepositoryConnectionString));
        Container.RegisterType<IFooValidator, FooInputValidator>("FooInputValidator");
        Container.RegisterType<IFooValidator, FooDataValidator>("FooDataValidator");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果不提供连接字符串，就不能创建和添加扩展。它的用法如下:

```
var fooCalculatorExtension = new FooCalculatorContainerExtension(configuration["connectionStrings:fooCalculator"]);
container.AddExtension(fooCalculatorExtension); 
```

Enter fullscreen mode Exit fullscreen mode

现在应用程序不太了解这个库，只知道它需要一个连接字符串。另一方面，这个库不知道使用它的应用程序在哪里或者如何存储它的连接字符串。(如果您曾经使用过这样一个库，它假定有一个 app.config/web.config，假定有一个特定的设置或连接字符串，并在它丢失时抛出一个无用的异常，您就会理解这是多么令人沮丧。)

如果涉及一组更复杂的设置，我可能会创建一个包含所有这些设置的类。使用。NET Core 你可以[绑定一个设置对象到你的配置](https://weblog.west-wind.com/posts/2017/Dec/12/Easy-Configuration-Binding-in-ASPNET-Core-revisited)。您可以提供一个 helper 方法来从 app.config/web.config 加载这些值

*   确保提供所需的值
*   抛出清晰、有用的异常，这样就没有人需要打开代码，弄清楚它在找什么，在哪里找

## 除非别无选择，否则不要调用构造函数，你总是有选择的

最后一点:阿迪容器的好处之一是它检查一个类的构造函数，确定它的依赖项是什么，并解析调用构造函数所需的依赖项。在大多数情况下，我们的注册不应该使用工厂方法，这些方法从容器中解析对象，然后将它们传递给另一个类的构造函数。

我到底在说什么？如果这毫无意义，或者你从未听说过这样的事情，现在停止阅读，因为你不需要看到这个:

```
protected override void Initialize()
{
    Container.RegisterType<ISomething, Something>();
    Container.RegisterType<ISomeOtherThing, SomethingElse>();
    Container.RegisterType<IFooRepository, SqlFooRepository>
        (new InjectionProperty("connectionString", _fooRepositoryConnectionString));
    Container.RegisterInstance(typeof(IFooValidator), "FooDataValidator", 
        new FooDataValidator(Container.Resolve<ISomething>()));
    Container.RegisterInstance(typeof(IFooValidator), "FooInputValidator", 
        new FooInputValidator(Container.Resolve<ISomeOtherThing>()));
    Container.RegisterInstance(typeof(IFooCalculator), 
        new FooCalculator(Container.Resolve<IFooRepository>(),
            new []
            {
                Container.Resolve<IFooValidator>("FooDataValidator"),
                Container.Resolve<IFooValidator>("FooInputValidator")}));
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，那会发生。我不知道为什么。如果所有的依赖项都已注册，就没有理由调用构造函数，向容器注册实例，解析它们，然后将它们传递给更多的构造函数。这就是容器为我们做的事情。使 DI 容器如此棒的部分原因是我们不必处理那种复杂性。当我们写代码时，没有嵌套依赖的级别。我们的类依赖于抽象，所以当我们使用一个类时，我们不必考虑这些抽象的实现依赖于什么。如果我们通过调用类的构造函数来“手动”承担构建类的责任，我们就失去了这种好处。

以上乱七八糟的功能与
相同

```
protected override void Initialize()
{
    Container.RegisterSingleton<ISomething, Something>();
    Container.RegisterSingleton<ISomeOtherThing, SomethingElse>();
    Container.RegisterSingleton<IFooRepository, SqlFooRepository>
        (new InjectionProperty("connectionString", _fooRepositoryConnectionString));
    Container.RegisterSingleton<IFooValidator, FooDataValidator>("FooDataValidator");
    Container.RegisterSingleton<IFooValidator, FooInputValidator>("FooInputValidator");
    Container.RegisterSingleton<IFooCalculator, FooCalculator>();
} 
```

Enter fullscreen mode Exit fullscreen mode

希望这有助于我们将依赖性注册类从巨大的、危险的野兽转变成一群易于管理的更小、更驯服的野兽，我们可以驾驭它们为我们服务，或者驾驭它们取得代码重构的胜利。或者两者都有！