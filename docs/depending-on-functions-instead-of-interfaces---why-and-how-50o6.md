# 依赖于函数而不是接口——为什么和如何

> 原文：<https://dev.to/scotthannen/depending-on-functions-instead-of-interfaces---why-and-how-50o6>

下面是一个简单的、人为设计的依赖于接口`IDoesMath`的类的例子，后面是修改后依赖于函数的同一个类。正如你将很快看到的，没有*直接的*利益。变化并没有使这个班变得更好。那么依赖函数而不是接口有什么好处吗？我认为，至少在某些情况下，依赖函数有可能提高代码的长期可维护性。(我还能更小心地修饰这句话吗？)

#### 依赖于一个接口

```
public class ClassThatDependsOnMath  
{ 
    private readonly IDoesMath _math; // interface

    public ClassThatDependsOnMath(IDoesMath math) 
    {
        _math = math; 
    }

    public Single Calculate(Single value1, Single value2)
    {
        return _math.DoMath(value1, value2);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 依赖于一个函数

```
public class ClassThatDependsOnMath
{
    private readonly Func<Single, Single, Single> _doMath; // function

    public ClassThatDependsOnMath(Func<Single, Single, Single> doMath)
    {
        _doMath = doMath;
    }

    public int Calculate(Single value1, Single value2)
    {
        return _doMath(value1, value2);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我看不出有什么明显的不同。这个接口实际上让我们更容易看到依赖项做了什么。(稍后将详细介绍。)那么，我们为什么要考虑第二种方法呢？

### 不同的功能会产生不同的效果

依赖功能至少有两个潜在的好处:

*   它实施了接口分离原则。您可以使用接口的一些方法，但不能使用其他方法。一个函数要么全有，要么全无。你要么用，要么不用。
*   它鼓励遵循单一责任原则。这并不完全意味着更小的类和更小的方法，但在实践中，这通常是结果。

我见过太多这样的例子，一个新的方法被添加到一个现有的接口和它的实现中，因为它很方便。然后添加另一个，再添加另一个，直到接口充满了没有意义的非内聚方法，并且实现它们的类是巨大的。最终，许多类依赖于这个接口，每个类只使用一两个方法。如果我们已经走上了这条黑暗的道路，那么对于每个依赖于这个接口的类来说，都存在着这样的风险:有人修改了这个类，就会找到一个理由向这个接口添加更多的方法。(一扇窗户被打破，混乱随之而来。)

这违反了接口分离原则，因为现在类依赖于接口，但是忽略了它的大多数方法。最终，我们需要为一个特定的场景创建一个新的接口实现，并且我们必须实现所有其他的方法，即使我们不需要它们。或者我们让我们不需要的方法不被实现，但这是邪恶的。

另一个副作用是，当我们为依赖于接口的类编写单元测试时，我们必须弄清楚它使用了哪些方法，这样我们就知道要模仿哪些方法。最终，我们会嘲笑那些我们甚至都不使用的方法。现在我们的单元测试很难阅读，因为它们被他们甚至不需要的代码污染了，我们不得不维护它们，因为实际上没有影响它们的变化。(你有没有改变过一个接口，发现自己在二十几个模拟中修复编译错误，你可能会使用也可能不会使用，因为这比确定你是否需要它们更快？我有。)

同时，如果接口已经失去控制，它的实现几乎肯定违反了单一责任原则。依赖它们的类也是如此。如果我们使用构造函数注入，我们可以看看一个类的构造函数，看看它依赖什么，我们可以告诉我们什么时候增加责任，因为我们增加了更多的依赖。向现有接口添加新方法会使其不那么明显。

依赖函数不一定能阻止所有这些，也不是唯一的解决方案。(也许我们只需要在接口名称中使用一个动词，这样就能更清楚地反映出它应该包含什么，不应该包含什么。)但它是一种防御手段，可以引导我们避免给接口增加臃肿，并在我们给类增加职责时显示出来。

但是它如何影响可读性以及我们配置依赖注入容器的方式呢？

### 委托 vs .功能或动作

在上面的例子中，我展示了一个依赖于数学函数的类:`Func<Single, Single, Single>`。如果我们正在进行构造函数注入，那么我们很可能使用依赖注入容器。在这种情况下，我们会遇到一些问题:
如果不同的类依赖于服务于不同目的但具有相同签名的函数，那该怎么办？
此外，我们的容器设置代码会令人困惑。如果我们注册了一个`Func<Single, Single, Single>`的实现，那么没有什么能告诉我们这个函数是做什么的，或者为什么有些东西依赖于它。

我们可以通过声明委托并依赖它们而不仅仅是函数签名来解决这两个问题。现在我们的数学例子可能是这样的:

```
public delegate int DoMath(Single value1, Single value2);

public class ClassThatDependsOnMath
{
    private readonly DoMath _doMath;

    public ClassThatDependsOnMath(DoMath doMath)
    {
        _doMath = doMath;
    }

    public int Calculate(Single value1, Single value2)
    {
        return _doMath(value1, value2);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们用容器注册实现时，我们注册的是`DoMath`的实现。它是显式的，就像我们注册接口实现时一样。

但是那个集装箱注册码是什么样子的呢？我必须知道它有多简单或者多难看，所以我用两个流行的容器进行了测试， [Autofac](https://autofac.org/) 和 [Castle Windsor](http://www.castleproject.org/) 。

在这两种情况下，我都想注册这个委托人

```
public delegate Single DoMath(Single value1, Single value2); 
```

Enter fullscreen mode Exit fullscreen mode

有了这个实现:

```
public class AddsNumbers 
{
    public Single Add(Single value1, Single value2)
    {
        return value1 + value2;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 向 Autofac 注册代表

假设我想解析容器中的所有内容(因为一个类实现可能有自己的依赖项需要解析)，我需要
注册类实现(只注册一次，而不是每个委托一次)
对于委托，注册一个解析类实现的函数，然后返回所需的方法。

看起来是这样的:

```
builder.RegisterType<AddsNumbers>();
builder.Register<DoMath>(c =>
{
    var componentContext = c.Resolve<IComponentContext>();
    var addsNumbers = componentContext.Resolve<AddsNumbers>();
    return addsNumbers.Add;
}); 
```

Enter fullscreen mode Exit fullscreen mode

没错，你要从 `IComponentContext`解析`IComponentContext` *。这并不漂亮，但是我们可以写一个扩展使它变得更好:* 

```
public static class AutofacBuilderExtensions 
{ 
    public static IRegistrationBuilder<TDelegate, SimpleActivatorData, SingleRegistrationStyle> RegisterDelegate<TDelegate, TSource>( 
        this ContainerBuilder builder,  
        Func<TSource, TDelegate> extractDelegate,  
        string sourceComponentName = null,  
        string registeredComponentName = null)  
        where TDelegate : class
    {
        var registrationFunction = new Func<IComponentContext, TDelegate>(context => 
        { 
            var c = context.Resolve<IComponentContext>(); 
            var source = sourceComponentName == null 
                ? c.Resolve<TSource>() 
                : c.ResolveNamed<TSource>(sourceComponentName); 
            return extractDelegate(source); 
        }); 

        return registeredComponentName == null ? 
            builder.Register(registrationFunction) : 
            builder.Register(registrationFunction) 
                .Named<TDelegate>(registeredComponentName); 
    } 
} 
```

Enter fullscreen mode Exit fullscreen mode

这包括一些额外的参数，以防我们需要指定一个依赖项的命名实例来解析，和/或我们想要为我们的新组件注册指定一个名称。现在组件注册看起来像这样:

```
builder.RegisterType<AddsNumbers>();
builder.RegisterDelegate<DoMath, AddsNumbers>(addsNumbers => addsNumbers.Add); 
```

Enter fullscreen mode Exit fullscreen mode

我可以接受这一点，特别是如果它有助于防止其他代码维护问题。

### 向温莎登记代表

过程是相同的，所以我将直接跳到扩展方法。

```
public static class WindsorRegistrationExtensions
{
    public static ComponentRegistration<TDelegate> RegisterDelegate<TDelegate, TSource>(
        this IKernel kernel, 
        Func<TSource, TDelegate> extractDelegate, 
        string sourceComponentName = null, 
        string registeredComponentName = null) 
        where TDelegate : class
    {
        var component = Component.For<TDelegate>().UsingFactoryMethod((kernel1, context) =>
        {
            var source = sourceComponentName == null ? kernel1.Resolve<TSource>() : kernel1.Resolve<TSource>(sourceComponentName);
            return extractDelegate(source);
        });
        kernel.Register(registeredComponentName == null ? component : component.Named(registeredComponentName));
        return component;
    }

    public static ComponentRegistration<TDelegate> RegisterDelegate<TDelegate, TSource>(
        this IWindsorContainer container, 
        Func<TSource, TDelegate> extractDelegate, 
        string sourceComponentName = null, 
        string registeredComponentName = null) where TDelegate : class
    {
        return container.Kernel.RegisterDelegate(extractDelegate, sourceComponentName,
            registeredComponentName);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

以及用法(在这种情况下包括组件名称):

```
Kernel.Register(Component.For<AddsNumbers>());
Kernel.RegisterDelegate<DoMath, AddsNumbers>(addsNumbers => addsNumbers.Add, registeredComponentName:"Adds"); 
```

Enter fullscreen mode Exit fullscreen mode

### 嘲讽一个代表

愚蠢的代表！你只是一种方法，没人喜欢你。(那永远不会老。)

当它与单元测试相关时，嘲讽又如何呢？假设我们有一个依赖于我们的`DoMath`委托的类，我们想要模仿那个委托，这样总是返回 99:

```
public class DependsOnMathFunction
{
    private readonly DoMath _doMath;

    public DependsOnMathFunction(DoMath doMath)
    {
        _doMath = doMath;
    }

    public Single ThisIsRedundantButDoMyMath(Single number1, Single number2)
    {
        return _doMath(number1, number2);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用一个匿名函数:

```
[TestMethod]
public void TestWithMockedDelegate()
{
    DoMath doMathMock = (value1, value2) => 99; // That was easy!
    var subject = new DependsOnMathFunction(doMathMock);
    var output = subject.ThisIsRedundantButDoMyMath(1, 2);
    Assert.AreEqual(99, output);
} 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果我们需要，我们可以使用最小起订量:

```
var doMathMock = new Mock<DoMath>();
doMathMock.Setup(x => x(It.IsAny<Single>(), It.IsAny<Single>())).Returns(99); 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用在测试类或静态类中声明的方法。

### 这如何影响我们实现依赖的方式？

这主要影响类如何“看到”它们所依赖的方法，而不是我们如何编写这些方法所在的类。然而，我们可能会看到两个不同之处:

首先，这些类可能不需要实现接口。我有写很多很多界面的习惯。有些人认为过多的界面是一种混乱。如果我们不打算使用接口，因为我们通过委托引用一个方法，那么我们不需要接口，为什么要创建它呢？

第二，委托指向的函数可以是静态的。我曾经认为静态方法是不好的，因为如果一个类调用一个静态方法，这个方法就不能被模仿或替换，这使得这个类更难进行单元测试。
我部分对，部分错。如果一个类直接调用一个静态方法*，那就是真的。
但是代表是一个可以被嘲笑的抽象概念。
静态函数只要不依赖或修改任何全局状态都可以。*

 *这使得我们的容器设置简单了很多，因为没有要注册的类或要解析的实例。

```
// Autofac
builder.Register<DoMath>(context => MyStaticMathClass.Add);

// Windsor
Kernel.Register(Component.For<DoMath>().Instance(MyStaticMathClass.Add)); 
```

Enter fullscreen mode Exit fullscreen mode

对于一些开发人员来说，依赖静态函数的能力是依赖委托或函数的一个更好的理由，而不是我提到的任何其他理由。这是迈向函数式编程的一步。它挑战了我编写和实现接口的根深蒂固的习惯，这感觉就像是我大脑的一个微小部分被重新布线。我喜欢这样。

## 导航到委托实现

一位读者很有帮助地指出，在 Visual Studio 中，导航到接口的实现很容易。我们如何导航到委托的实现？阅读[依赖功能而非界面——导航问题](http://scotthannen.org/blog/2018/04/09/depending-on-functions-navigation.html)。

* * *

最初发布于[scotthannen.org](http://scotthannen.org/blog/2018/03/18/depending-on-functions-instead-of-interfaces.html)。*