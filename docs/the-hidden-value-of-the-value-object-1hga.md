# 值对象的隐藏值

> 原文：<https://dev.to/rafalpienkowski/the-hidden-value-of-the-value-object-1hga>

# 简介

在我听完沃恩·弗农的“实现领域驱动设计”之后，我决定写一篇与 DDD 有关的文章，引起人们对被低估的价值对象的关注。根据我的经验，开发人员在他们的项目中滥用实体。通常，这种行为会导致出现许多缺乏业务逻辑的领域模型。在我看来，一些缺乏活力的领域模型可以很快转化为价值对象。

注意:所有的例子都是用 C#写的，并且使用了最新的 C#语法。我希望对于使用不同语言的开发人员来说，它们也很容易理解。

### 举例:

让我们从汽车实体的简单示例开始，如下面的代码片段所示。

```
public class Car
{
    /* Properties */
    public string Color { get; set; }
    public bool Metallic { get; set; }

    public string FuelType { get; set; }
    public int EngineCapacity { get; set; }
    { ... }

    /* Methods */
    public void Drive(int distance) { ... }
    { ... }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将关注与汽车颜色相关的属性。我指的是颜色和金属属性。为简化起见，颜色由字符串类型表示。

# 价值对象的特征:

* * *

### 1)度量、量化或描述一个领域的主题

在我们的例子中，`Color`和`Metallic`是值对象的最佳候选对象。两者都在描述车辆的外观。所以提取新的类:

```
public class Color
{
    public string Hue { get; set; }
    public bool Metallic { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

每当`Hue`或`Metallic`改变时，我们会得到一个新的颜色，所以让我们进入下一个点。

### 2)是不可变的

不变性意味着对象状态在初始化后不能改变。在 Java 或 C#中，这可以通过将所有参数传递给值对象的构造函数来实现。基于参数值，将设置对象的状态。当然，基于这些值，可以计算出一些额外的属性。
*注意:*对象本身既不能通过公共方法调用属性设置器，也不能通过私有方法调用。这是禁止的。

让我们根据前面的想法来增强我们的示例。

```
public class Color
{
    public Color(string hue, bool metallic)
    {
        Hue = hue;
        Metallic = metallic;
        if (hue == "white" && !metallic)
        {
            BasicPallete = true;
        }
     }

    public string Hue { get; }
    public bool Metallic { get; }
    public bool BasicPallete { get; }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们已经计算了`BasicPallete`属性。例如，它可能会影响汽车的价格计算。

### 3)将链接的属性值组成整数单元

这意味着所有属性都是有界的，每个属性都提供了描述对象整体价值的关键信息部分。单个属性值不能提供关于对象的全面信息。在我们的例子中，关于一种颜色是否是金属色的知识不能给我们关于它的色调的信息。同样的情况是当我们知道色相，却不知道是不是金属色的时候。

> 在这一点上，沃思是一个经常被引用的完美例子。让我们以 100 美元为例。货币和金额是联系在一起的。让我们想象一下，在我们的对象中，有人将货币从美元换成了 MXN(墨西哥比索)。这一变化具有重大影响。100 MXN 大约相当于 5.25 美元。有人想成为这种小变化的受害者。

### 4)在测量值变化的情况下是可替换的

在我们的例子中，汽车是一个实体，颜色是我们领域中的一个价值对象。有一天我们决定改变我们汽车的颜色。假设我们买了一辆绿色非金属汽车，但是几年后，我们想要一辆绿色金属汽车。我们不能只是给现有的画增加金属光泽。我们需要用新的绿色金属漆重新粉刷。

```
Color color = new Color("green", false);
//color.Metallic = true;  -> This is not allowed

color = new Color("green", true); 
```

Enter fullscreen mode Exit fullscreen mode

### 5)可以与其他价值对象相比较

在像 C#这样的语言中，当我们试图比较两个默认为的对象[时，比较会覆盖两个对象在内存中的位置(称为](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons)[引用相等](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-test-for-reference-equality-identity))。两个对象可以有相同的属性值，但不会相等。这是关于值对象的错误假设。在这种情况下，我们需要给定类型和所有属性值相等(称为[值相等](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-test-for-reference-equality-identity))。

根据我们的例子，两种颜色是相同的，如果它们具有相同的色调，并且是金属色或不是金属色。两辆绿色和金属色的车颜色一样。

所以让我们增强我们的类，实现值相等:

```
public class Color
{
    public Color(string hue, bool metallic)
    {
        Hue = hue;
        Metallic = metallic;
        if (hue == "white" && !metallic)
        {
            BasicPallete = true;
        }
    }

    public string Hue { get; }
    public bool Metallic { get; }
    public bool BasicPallete { get; }

    public override bool Equals(object obj)
    {
        return this.Equals(obj as Color);
    }

    public bool Equals(Color otherColor)
    {
        if (Object.ReferenceEquals(otherColor, null)) return false;
        if (Object.ReferenceEquals(this, otherColor)) return true;
        if (this.GetType() != otherColor.GetType()) return false;

        return (Hue == otherColor.Hue) && (Metallic == otherColor.Metallic);
    }

    public static bool operator ==(Color leftColor, Color rightColor)
    {
        if (Object.ReferenceEquals(leftColor, null))
        {
            // null == null = true
            return (Object.ReferenceEquals(rightColor, null));
        }
        return leftColor.Equals(rightColor);
    }

    public static bool operator !=(Color leftColor, Color rightColor)
    {
        return !(leftColor == rightColor);
    }

    public override int GetHashCode()
    {
        return (Hue.GetHashCode() * 0x100000) + (Metallic.GetHashCode() * 0x1000) + BasicPallete.GetHashCode();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 6)具有无副作用的行为

这是根本法则。如果没有它，值对象可能被视为简单的属性容器。要理解它，我们应该从理解无副作用函数开始。没有场地效应的函数有结果，但不修改其状态。这种函数是函数式编程范例中基础。

值对象公开的所有方法应该是一个没有副作用的函数，这不违反值对象的不变性。

我们以汽车的重新喷漆为例。我们可以通过在类中添加这样的函数来实现重画。

```
public class Color
{
    public Color(string hue, bool metallic)
    {
        Hue = hue;
        Metallic = metallic;
        if (hue == "white" && !metallic)
        {
            BasicPallete = true;
        }
     }

    public string Hue { get; }
    public bool Metallic { get; }
    public bool BasicPallete { get; }

    public Color Repaint(string hue, bool metallic)
    {
        return new Color(hue, metallic);
    }

    // Value Equality part
    {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，有一个类似的效果，就像汽车的重新喷漆。但是方法和值对象之间的联系更强，并且没有副作用。我们不修改颜色对象的状态。取而代之的是，我们创造了一个新的有需求值的。

```
Color color = new Color("green", false);
color = color.Repaint("green", true); 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

当然，我没有涵盖与值对象相关的所有主题，尤其是 DDD，但是我希望这篇文章能够激励您在项目中更多地使用它。

[![Keep calm and use Value Objects](img/09afddaa3d40bf8d8d997f833286a8ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j5DGy04m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rafalpienkowski/resources/master/hidden-value-of-the-value-object/keep-calm.png)

**附言**

请不要从一个极端走向另一个极端。

# 链接:

*   [Color.cs 类](https://github.com/rafalpienkowski/resources/blob/master/hidden-value-of-the-value-object/Color.cs)
*   [Color.cs 只读结构](https://github.com/rafalpienkowski/resources/blob/master/hidden-value-of-the-value-object/ColorStruct.cs)
*   [实现值对象](https://docs.microsoft.com/en-us/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/implement-value-objects)从。网络指南
*   [来自 MartinFowler.com 的伊文思分类](https://martinfowler.com/bliki/EvansClassification.html)
*   来自 MartinFowler.com 的[值对象](https://martinfowler.com/bliki/ValueObject.html)，其中包含用 Java 编写的示例