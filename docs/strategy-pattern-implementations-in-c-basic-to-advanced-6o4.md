# 在 C#中实现策略模式的 6 种方法(从基础到高级)

> 原文：<https://dev.to/jamesmh/strategy-pattern-implementations-in-c-basic-to-advanced-6o4>

*注:[见我博客](https://www.blog.jamesmichaelhickey.com/strategy-pattern-implementations/)的规范帖子 T3】*

让我们看看在 C#中实现策略模式的一些不同方式。首先，我想简单提一下为什么我们关心设计模式，以及策略模式适合在哪里。

# 为什么要知道策略模式？

作为软件开发人员和/或软件架构师，理解设计模式是一项重要的技能。如果你不这样做，你最终会:

*   浪费时间解决社区已经解决的问题
*   创建可能难以维护的代码
*   编写可能难以推理的代码
*   构建潜在难以扩展的软件组件和片段

策略模式是一种处理问题的方法，在这种情况下，你有不同的逻辑路径可用，并且基于某些条件，你需要选择这些路径中的一个。换句话说，您有太多的`if`或`switch`案例，需要一个更干净、更具可扩展性的替代方案。

有关设计模式的更多信息:

*   [亚马逊上的原版“设计模式”书籍](https://amzn.to/2WP2P8w)
*   亚马逊上一本更容易理解的书——《头先设计模式:大脑友好指南》

# 策略模式的流程

策略模式遵循一个基本流程:

1.  入口点接受(用户、系统等)做出的选择。)
2.  基于这种选择，从许多算法或逻辑路径中选择一个来执行。
3.  执行选定的算法。

# 战略模式的实际用途

一个实际的例子，一些(大多数？)web 开发人员所面临的是构建面向业务的报表系统。web 应用程序中的报告系统是策略模式的一个很好的用例:

*   用户可以从可用报告列表中选择要执行的特定报告
*   用户可以导出不同格式的报告
*   等等。

想象一个有 100 种报告可供选择的系统。你可以在你的网页上创建 100 个链接。你可以使用 100 条`if`语句。100 `switch`例。但是，如果用户现在可以选择每天自动发送到电子邮件中的报告，那会怎么样呢？现在不能用链接了。那些`if`语句变得相当混乱和不可维护——特别是当新的需求导致嵌套的`if`语句时。**还有一种更好的方式——战略模式。**

# 基本实现

让我们看一个非常基本的实现。我将使用一个非常“愚蠢简单”的控制台应用程序计算器的例子，它有 4 个运算(总是两个数字):

*   增加
*   减去
*   乘；成倍增加；（使）繁殖
*   划分

这是为了避免在试图掌握这种模式时分心，这种模式可以应用于更复杂的业务问题。

下面是基本的例子:

```
// We have 4 methods that perform a calculation using 2 integers.
// These are our available algorithms to select from.
private static int Add(int a, int b) => a + b;
private static int Minus(int a, int b) => a - b;
private static int Multiply(int a, int b) => a * b;
private static int Divide(int a, int b) => a / b; 
```

忽略用户输入和输出，在同一个类中你会有“策略”:

```
switch (userChoice)
{
    case "+":
        result = Add(numOne, numTwo);
        break;
    case "-":
        result = Minus(numOne, numTwo);
        break;
    case "*":
        result = Multiply(numOne, numTwo);
        break;
    case "/":
        result = Divide(numOne, numTwo);
        break;
} 
```

这种实现是:

*   快的
*   容易的
*   简单的

但是随着更多的算法——比方说——更多，保存所有这些方法和逻辑的文件和/或类将变得非常臃肿。并且 switch 语句开始变成一个怪物...

##### 什么时候用？

这种实现是:

*   对于那些从模式开始的人来说，这是很好的一步
*   使用非常少的选择很简单

# 基本同开关语句删除

这个过程的下一步是清理`switch`语句。太啰嗦了——即使是对于选择不多的地方。`switch`变成了

```
Dictionary<string, Func<int, int, int>> strategies = new Dictionary<string, Func<int, int, int>>() {
    { "+", Add }, // Holding a reference to the method Add()
    { "-", Minus },
    { "*", Multiply },
    { "/", Divide }
};

Func<int, int, int> selectedStrategy = strategies[userChoice]; // userChoice is either "+", "-", "*" or "/"
int result = selectedStrategy(numOne, NumTwo); 
```

通过使用字典，而不是显式地调用我们想要的方法，我们存储一个对`key`和`value`的查找——`value`是对我们想要为那个`key`调用的方法的引用。

##### 什么时候用？

这种方式迫使您创建其他方法来处理每种情况，而不是在当前范围内内联更多的逻辑。在下列情况下，您可能希望使用此实现:

*   您有一些希望不要太冗长的`if`或`switch`语句
*   你有“少数”算法可供选择

对于某些人来说，字典定义可能太冗长了。在这种情况下，我们可以包装每个方法(加、减等。)中的一个`Action`(即λ)像`() => Add(numOne, numTwo)`。不过，我们稍后会研究如何使用 lamdas...

# 使用接口

下一步是清理方法列表并将其一般化。如果我们在系统中的某个地方有另一个类，它需要对我们选择的算法可用，那会怎么样？不是说这是一个好的设计实践，但事情发生在现实世界中。

这里我们要做的是使用一个接口来抽象实现。这通常被认为是策略模式的“适当”实现。

**首先**，我们创建一个新的接口来抽象我们的数学运算:

```
public interface IMathOperator
{
     int Operation(int a, int b);
} 
```

**接下来**，我们实现每个算法的接口(这里有一个例子):

```
public class MathAdd : IMathOperator
{
    public int Operation(int a, int b)
    {
        return a + b;
    }
} 
```

**最后是**，我们可以在调用代码
中使用接口类型`IMathOperator`

```
Dictionary<string, IMathOperator> strategies = new Dictionary<string, IMathOperator>() {
    { "+", new MathAdd() },
    { "-", new MathSubtract() },
    { "*", new MathMultiply() },
    { "/", new MathDivide() }
};

IMathOperator selectedStrategy = strategies[userChoice];
int result = selectedStrategy.Operation(numOne, numTwo); 
```

现在我们能够将每个算法分离到它自己的类/文件中。这允许我们查看我们的文件系统，并更好地了解我们的系统能做什么。而且，它更多地分解了我们的代码，从而减少了每个文件的视觉噪声。

##### 什么时候用？

由于这是最常见的模式实现，您可能会使用此版本，因为:

*   当与开发团队一起工作时，这很有帮助(要么他们已经理解了模式，要么在研究模式时，这可能是他们将会遇到的)
*   算法的数量正在扩大
*   你希望每个算法都在自己的文件/类中(也许每个算法真的很复杂，等等。)

同样，您可以更好地了解您的系统，因为每个算法都在自己的文件中。使用接口，您还可以为现有的类实现这种抽象提供一些灵活性。

# 昂贵的对象实例化？

上述方法的一个问题是，我们正在实例化我们从中选择的所有类。如果实例化这些对象的开销很大怎么办？即使不是这样，使用下面的方法也是一种可以提高性能的快速修复方法——尤其是当我们有很多实现可供选择的时候。

```
// Each key now returns a function that will instantiate an object when executed.
Dictionary<string, Func<IMathOperator>> strategies = new Dictionary<string, Func<IMathOperator>>() {
    { "+", () => new MathAdd() },
    { "-", () => new MathSubtract() },
    { "*", () => new MathMultiply() },
    { "/", () => new MathDivide() }
};

IMathOperator selectedStrategy = strategies[userChoice](); // Invoke the Func to instantiate new object 
```

##### 什么时候用？

你使用了字典技巧...

*   您正在使用昂贵的对象(例如，作为继承层次结构一部分的对象，在实例化时执行一些昂贵操作的对象，保存大量数据的对象，等等。)
*   有许多算法可用(并且不想分配所有额外的内存)

# 动态实例化

您可能已经注意到，策略模式基本上与工厂模式相同。OOP 中的工厂模式通常遵循以下流程:

1.  决定选择更高层次抽象的具体类型(接口、抽象类等)。)
2.  实例化它
3.  返回新对象。

策略模式是相似的，它可以做上面流程中的所有事情，但是增加了一个步骤:

*   调用某个抽象方法的具体实现(即从具体对象调用接口的方法)。

这让我们想到，我们可以使用工厂模式中使用的高级技术来增强我们的策略模式实现。其中之一是基于类/类型的名称动态实例化对象。在我们的 C#实现中，我们可以用这个替换字典，假设我们的用户输入现在是“Add”而不是“+”，等等。:

```
IMathOperator selectedStrategy = GetMathOperatorFromFullClassPath($"Strategy.InterfacesExample.Math{userChoice}"); 
```

工厂方法:

```
 private static IMathOperator GetMathOperatorFromFullClassPath(string fullClassPath) => 
         // The true's mean throw exception and ignoreCase.
         Activator.CreateInstance(Type.GetType(fullClassPath, true, true)) as IMathOperator; 
```

如果我们知道具体实现的名称空间，那么我们就可以根据用户输入或数据库结果等动态地实例化我们想要的那个。

##### 什么时候用？

*   你有许多具体的类型
*   您希望将工厂逻辑概括得更加简洁
*   快速添加新的实现，因为您不需要再次在工厂中挖掘(例如，只需添加一个新的类，并根据该类的名称为用户选择)

缺点是:

*   您引入了更多运行时错误的可能性
*   需要更仔细地考虑错误处理
*   对于项目的新手来说，代码可能不那么直观
*   未来的重构会变得更加困难，因为你找不到可能用到你的类的地方的引用

# 最后一名:先进工厂

最后一步！让我们介绍一种更高级/动态的方法，使用反射来动态地“发现”我们的策略模式中可能需要的具体实现。但是在 C#中使用反射我们可以找到当前程序集(即项目或库)中实现我们正在寻找的接口(IMathOperator)的所有类型。下面是这个策略的样子:

```
// I.e. I want a "IMathOperator" object with the class name "Math[Add]" or "Math[Subtract]", etc.
IMathOperator selectedStrategy = Instantiate<IMathOperator>($"Math{userChoice}");
int result = selectedStrategy.Operation(numOne, numTwo); 
```

将工厂功能重构为更灵活/可重用的部分，我们得到:

```
private static T Instantiate<T>(string className)
{
    Type typeImplemented = typeof(T);
    Type selectedType = Assembly.GetExecutingAssembly() // i.e. our project/library
            .GetTypes()
            .First(t => typeImplemented.IsAssignableFrom(t) && t.Name == className);

    return (T) Activator.CreateInstance(selectedType);
} 
```

我们可以简化它，只通过类名过滤所有的程序集类型。我们只需要更加注意整个项目中所有名称空间下的命名冲突。

##### 什么时候用？

*   您想要一个可以在任何地方使用的可重用工厂函数
*   你有大量具体的策略可用
*   您在其他地方使用策略模式，并且想要一个更通用的机制来处理它们

骗局

*   潜在的运行时错误
*   类之间命名冲突的可能性(例如，如果您不注意它们实现的类型，不同名称空间中的两个类名“MathAdd”可能会导致运行时错误)
*   对于项目的新成员来说，这一部分如何工作并不明显

# 结论

**TL；DR** 该策略模式有助于消除越来越多的`if`和`switch`案例。通过使用接口，或者在更简单的情况下，简单的函数(即静态方法)，我们可以引入一种更灵活、可维护和可预测的方式来选择特定的算法或逻辑路径。

我的偏好是使用字典将可能传入值的键(“输入”)映射到要选择和执行的相应接口或方法。

使用反射来实现这一点可以提供一些便利，或许还可以提供更多的灵活性，但是失去对具体实现使用位置的所有引用的跟踪(因为具体的实现是动态实例化的)是一个相当大的问题。

# 反馈？

**有何反馈或意见？**我很想听听你的想法，或者我是否错过了什么等等。

# 保持联系

别忘了在 [twitter](https://twitter.com/jamesmh_dev) 或 [LinkedIn](https://www.linkedin.com/in/jamesmhickey/) 上联系我！

# 导航您的软件开发生涯

一封电子邮件简讯，我将在其中回答订阅者的问题，并就以下主题提供建议:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？

听起来有趣吗？加入社区吧！