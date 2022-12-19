# 解决任何语言的表达问题

> 原文：<https://dev.to/kspeakman/solving-the-expression-problem-in-any-language-289m>

我从[这篇关于表情问题的精彩帖子](https://code.camdenreslink.com/dev/the-expression-problem-in-dot-net/)中得到启发。这很好地解释了 OO(面向对象)和 FP(函数式编程)范例如何各自解决了表达式问题的一半，但不是全部。我鼓励你阅读这篇文章。

但如果你没有，这里是我对问题约束的解释(可能不准确)。

1.  **在不改变现有行为的情况下向类型添加新的数据案例**
2.  **在不改变现有数据的情况下为类型添加新行为**

在面向对象的范例中，你可以做到第一，但不能做到第二。但是在 FP 范式中，你可以实现#2，但不能实现#1。再次，检查上面链接的帖子的优秀例子。

## 横着看问题

我们在办公室有一个共同的口号。如果一个问题变得很难用代码实现，后退一步，从不同的角度来看它。

*通常是商业视角...但是我跑题了。*

在我读了帖子并反思了表达问题后，我意识到我们已经解决了它(因为我们不得不)...这种方式几乎可以在任何模式下工作...不使用特定于语言的特性，如类型类或多方法，而是使用消息传递模式。或者更确切地说，将数据案例视为我们可能(也可能不会)处理的消息。

下面我将展示如何用这种方法在 FP 和 OO 语言中解决[表达式问题](https://en.wikipedia.org/wiki/Expression_problem)的例子。我会用。NET 语言，所以你知道他们没有任何我们通常看到的解决表达式问题的真正酷的技巧。

## F#举例

让我们从函数优先语言 F#中的 Shape 类型开始。

```
type Shape =
    | Circle of radius:double
    | Rectangle of height:double * width:double
    | Triangle of base:double * height:double 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们定义一个形状的`area`行为。

```
module ShapeCalc =
    let area shape =
        match shape with
        | Circle radius ->
            Some (Math.PI * radius * radius)
        | Rectangle (height, width) ->
            Some (height * width)
        | Triangle (base, height) ->
            Some (base * height * 0.5)
        | _ ->
            None 
```

Enter fullscreen mode Exit fullscreen mode

这非常接近于在 F#中表达类型和行为的正常方式。除...之外...

牛仔，坚持住！这是**函数式编程**，你不需要像 FP ~~象牙塔~~原则所说的那样详尽地匹配每一个案例。

如果你想解决第一个表达问题，这是关键。您不能要求所有数据案例都匹配...只有你知道的人。事实上，这目前做了一个详尽的匹配，但是增加了一个额外的`_`匹配来处理还没有实现的情况。

"*您正在返回一个`Option`，但是我不想被 None 的情况所困扰！*

上个问题你*刚才*不是引用了 FP 象牙塔吗？...好了，那么这里是关于解决表达问题的重要一点。当你*真的*需要在不触及现有功能的情况下添加新的案例时，你也必须预料到现有的功能不知道如何处理每一个案例。(而且他们可能也不需要这么做。)所以你必须准备好处理 None 或者空 list 之类的东西，代表被忽略案例的结果。

“*哈！我就知道有圈套！*

你说得对。解决了表达问题，并不导致神奇的独角兽开发商天堂土地。它有自己的一套权衡。真正的问题是，通常编译器会强迫你处理数据案例和行为的每一种组合。如果你漏掉了一个，就会有一个编译错误，上面有你的名字。因为这在某些场景中是痛苦的和不可取的，我们通过只声明我们知道如何处理并告诉编译器不要担心其余的来解决这个问题。但这也意味着编译器不能警告我们丢失的组合。因此，有所取舍。

好了，现在让我们暂停提问，展示当我们添加新的数据案例时会发生什么。*提示:没什么。*

```
type Shape =
    | Circle of radius:double
    | Rectangle of height:double * width:double
    | Triangle of base:double * height:double
    // we just added Ring
    | Ring of radius:double * holeRadius:double 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了一个新的 case，但是先前定义的`area`函数仍然像以前一样编译并继续工作。我们可能实际上想要在`area`中处理新的`Ring`案例，但是这可以在以后完成，并且独立于添加新的数据案例。 **#1 取得**。所以现在让我们在不涉及类型的情况下添加一个新的行为。

```
module ShapeCalc =
    let area shape =
        ... // same as before

    // we just added circumference
    let circumference shape =
        match shape with
        | Circle radius ->
            Some (Math.PI * 2 * radius)
        | Ring (radius, _) ->
            Some (Math.PI * 2 * radius)
        | _ ->
            None 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`circumference`函数实际上只对圆形有意义——否则我会称它为“周长”。所以我只处理我所知道的圆形。我不需要接触 Shape 类型就能编译它。并且以后添加新的形状也不会击垮我。 **#2 达成。**

我们解决了 F#中的表达式问题。现在我们来看看 C#。

## C#示例

首先，让我们定义形状类型。

```
public interface IShape {}

public class Circle : IShape
{
    public double Radius { get; set; }
}

public class Rectangle : IShape
{
    public double Height { get; set; }
    public double Width { get; set; }
}

public class Triangle : IShape
{
    public double Base { get; set; }
    public double Height { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

我可以在这里使用一个空的基类，但是我选择使用一个名为`IShape`的[标记接口](https://en.wikipedia.org/wiki/Marker_interface_pattern)。它只是一个没有成员的接口。就像从基类继承一样，它允许我在一个基类后面表示多个形状。

现在让我们看看如何制作可扩展的行为，从`Area`开始。

```
public class ShapeCalc
{
    public static double? Area(IShape shape)
    {
        switch (shape)
        {
            case Circle c:
                return Math.PI * c.Radius * c.Radius;
            case Rectangle r:
                return r.Height * r.Width;
            case Triangle t:
                return t.Base * t.Height * 0.5;
            default:
                return null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这依赖于 c# 7——现在使用 syntax Sugar(TM)进行类型匹配。

“*这违反了 OO ~~血誓~~的原则！火焰！！1!*

好吧，你抓到我了。OO 原则说隐藏实现细节(又名字段)。并且不要让外部对象依赖于实现细节。然而，适当的 OO 原则不能满足第二个表达问题。也就是说，在一个适当的面向对象实现中，当我想给一个有多个子类的类型添加一个新行为时，我必须接触每个子类**，即使只是添加一个抛出`NotImplementedException`的粗略方法。**

所以总得有所让步。在我的演绎中，这就是。我们没有使用惯用的“对象”，而是使用 [DTO](https://en.wikipedia.org/wiki/Data_transfer_object) 模式来表示消息数据。然后，我们不再使用基于变异的类方法，而是使用静态函数。*毕竟，我确实打算通过使用消息传递风格来解决表达问题，而不考虑语言。*

现在，我们添加一个新的数据案例。

```
... // previous classes

// we just added Ring
public class Ring : IShape
{
    public double Radius { get; set; }
    public double HoleRadius { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Area`仍不加修改地编译。我们可能确实希望`Area`知道`Ring`，但是它可以在以后添加，独立于添加`Ring`类型。 **#1 取得**。所以现在让我们为 IShape 添加一个新的行为。

```
public class ShapeCalc
{
    public static double? Area(IShape shape)
    { ... } // same as before

    // we just added Circumference
    public static double? Circumference(IShape shape)
    {
        switch (shape)
        {
            case Circle c:
                return Math.PI * 2 * c.Radius;
            case Ring r:
                return Math.PI * 2 * r.Radius;
            default:
                return null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了新的行为，而不必同时修改任何 IShape 类型。 **#2 达到**。

## 我用这个样式做什么？

我在已经想使用消息传递模式的地方使用它。示例:当我的逻辑代码做出决定时，它不会产生副作用。相反，它返回消息来表示决策。事实上，我们首先存储的是决策。(请注意，我随意创造了一个新术语:请看**决策采购**！😆)我们目前称这些决策消息为事件，以区别于其他类型的消息。然后，我们可能还会根据这些决定产生其他副作用。

稍后，其他组件从事件数据库中读取并处理它们关心的事件，以承担各自的责任。(例如:发送电子邮件)。如果我向系统添加一个新事件，现有的行为不会受到影响。他们不需要知道这件事，所以他们只是忽略它。如果我添加一个新的侦听器，事件类型不会中断。

## 告诫

同时拥有类型和行为的可扩展性——表达式问题的答案——不会将开发环境转变成独角兽乌托邦。有时旧的函数应该处理新的数据案例。但是编译器不会再告诉你了。所以现在轮到你了，亲爱的程序员，*知道*需要做什么，并且*记得*去做。恐怖啊！

我想这就是为什么自从“问题”被首次提出以来，大多数语言都得到了发展...他们没有费心去解决这个问题。大多数被提出来解决“问题”的特性通常是小众的、很少使用的特性。也许这是一个你不想解决的问题，除非你真的不得不解决。

也许类似于[分布式对象第一定律](https://www.martinfowler.com/bliki/FirstLaw.html)...

> **表达式问题第一定律**
> 
> 1.  不解决表达问题。

我希望你喜欢这篇文章。❤️

/∞