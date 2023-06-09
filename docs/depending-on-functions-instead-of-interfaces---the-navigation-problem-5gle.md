# 依赖于功能而不是界面——导航问题

> 原文：<https://dev.to/scotthannen/depending-on-functions-instead-of-interfaces---the-navigation-problem-5gle>

一位读者在之前的一篇文章中指出了一个重要的未解决的问题，[依赖于功能而不是接口——为什么以及如何](https://dev.to/scotthannen/depending-on-functions-instead-of-interfaces---the-navigation-problem-5gle)。帖子中显示的代码比使用界面的代码更难导航。如果一个类依赖于一个接口，你可以使用 CTRL-F12 或者右击接口并选择“转到实现”来导航到 Visual Studio 中的实现(假设它们在同一个解决方案中)

但是假设我们有一个依赖于委托的类:

```
public class ClassThatDependsOnMath
{
    private readonly DoMath _doMath; // <-- The delegate

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

而 delegate 的实现就是这个方法:

```
public class Calculator 
{
    public Single AddNumbers(Single value1, Single value2)
    {
        return value1 + value2;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在看`ClassThatDependsOnMath`，我们看到它依赖于`DoMath`，我们想导航到`DoMath`的实现，我们该怎么做呢？我们可以到达那里，但是路程有点长。我们必须找到委托的用法，这将引导我们找到向依赖注入容器注册它的代码，并阅读它以查看注册的实现是什么。

两个因素在一定程度上缓解了这个问题:

*   即使我们可以导航到一个接口的实现，我们可能仍然需要访问依赖注入代码来确定我们正在查看的类使用了哪个实现。然而，很多时候只有几个实现，我们已经知道我们在寻找哪一个，所以从界面导航到实现的能力仍然很方便。
*   无论我们依赖于接口还是委托，如果它在应用程序的源代码中不可用，我们就不能导航到它的实现。

所以这不是世界末日，但还有改进的空间。我们怎样才能让它变得更好？通过让我们实现委托的方法显式地实现它。这与实现一个接口并没有太大的不同，除了它是方法，而不是类。

```
public class Calculator 
{
    public readonly DoMath AddNumbers = (Single value1, Single value2) => value1 + value2;
} 
```

Enter fullscreen mode Exit fullscreen mode

从技术上讲，我们甚至可以从函数声明中省略这些类型，因为它们已经被委托指定了。我更喜欢把它们留在里面，这样我就可以在查看方法时看到类型。

```
public class Calculator 
{
    public readonly DoMath AddNumbers = (value1, value2) => value1 + value2;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以找到委托(Shift+F12)的用法，这将引导我们向 DI 容器注册它，并实现它。Resharper 提供了“查找高级用法”(Ctrl+Shift+Alt+F12)，其中包括查找代理目标的选项。

这是不同的，不同是一个问题，因为这意味着开发人员将看到一些不符合熟悉惯例的东西。这种担忧有多大取决于你和你的团队。

如果你读了前一篇文章，你会知道我没有买依赖于委托和接口的股票。它只是在某些情况下有好处，例如当您想要防止向现有接口中不受控制地添加方法时，它们的实现的相应增长，以及它允许使用接口的类添加职责而不显式添加依赖项的相互依赖方式。一方的成长导致另一方的成长。这不是接口本身的问题，只是启用了看似很小的快捷方式，这些快捷方式累积成了具有复杂依赖关系的大类。我把*相互依赖*和*使*联系在一起，因为这让我想起那种不健康的关系。对一个类的依赖关系的可见添加应该有助于防止该类中责任的累积。但是太多时候我们的界面说，“没关系，只是把你额外的责任藏在我里面。违反单一责任原则没问题。我们一起来做。”

一位代表脚踏实地地说:“你可以想多粗心就多粗心，但我不会帮你隐瞒你在做什么。”除非，我们开始向委托的签名添加更多的参数，或者向它的输入或输出添加属性，只是因为一个实现需要它们。这导致了另一个反模式，即不一致填充的对象。稍后会详细介绍。