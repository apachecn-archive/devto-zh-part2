# 选角糟透了。泛型类更糟糕。

> 原文：<https://dev.to/anotherdevblog/casting-stinks-generic-classes-are-worse-567a>

让我们在课堂设计中进行一次想象的旅行。

假设您有以下表示车辆的类。你可以采用多态方法，要么编写它们从一个公共基类继承，要么实现一个公共接口:

```
public abstract class Vehicle
{
  public void Move() { }
  public abstract int PassengerCapacity { get; }
  // ...
}

public class Sedan : Vehicle
{
  public override int PassengerCapacity => 4;
  public bool IsConvertible { get; set; }
  public int TireSize { get; set; }
  // ...
}

// Other classes for motorcycle, truck, boat, etc. 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利，但这些都是复杂的物体，需要工厂来制造。它们变化很大，每个都需要自己的定制工厂、装配线、装配线配置和工程师。所以你为这些实体创建基类或接口:

```
public abstract class VehicleAssemblyLineInstructions
{
  public int MaxNumberOfUnitsToBuildInParallel { get; set; }
  // ...
}

public abstract class VehicleEngineer
{
  public string Name { get; set; }
  // ...
}

public abstract class VehicleAssemblyLine
{
  public VehicleEngineer LeadEngineer { get; set; }
  public abstract Vehicle Build(VehicleAssemblyLineInstructions instructions);
}

public abstract class VehicleFactory
{
  public string FactoryName { get; set; }
  public VehicleAssemblyLine AssemblyLine { get; set; }
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

既然基类或接口已经存在，那么接下来就要创建具体的实现:

```
public class SedanAssemblyLineInstructions : VehicleAssemblyLineInstructions
{
  public int AirbagSafetyRating { get; set; }
  // ...
}

public class SedanEngineer : VehicleEngineer
{
  public bool IsCertifiedByFord { get; set; }
  // ...
}

public class SedanAssemblyLine : VehicleAssemblyLine
{
  public string SedanMakersUnionType { get; set; }
  public abstract Vehicle Build(VehicleAssemblyLineInstructions instructions)
  {
    // TODO: Implementaton here
    throw new NotImplementedException();
  }
}

public class SedanFactory : VehicleFactory
{
  public string SedanManufacturerName { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

好，这是我们遇到的第一个问题。在您的具体实现的 build 方法中，为了访问汇编指令或工程师的轿车特定成员，您需要将它们转换为特定类型。谁知道他们会不会投。编译器不会强制它们传递你想要的:

```
public class SedanAssemblyLine : VehicleAssemblyLine
{
  public abstract Vehicle Build(VehicleAssemblyLineInstructions instructions)
  {
    // The compiler enforces that I have a VehicleAssemblyLineInstructions object. No idea whether the caller knew my
    // inner workings enough to guess that I really want a SedanAssemblyLineInstructions. Guess we'll find out now!
    SedanAssemblyLineInstructions sedanAssemblyInstructions = (SedanAssemblyLineInstructions)instructions;
    SedanEngineer sedanEngineer = (SedanEngineer)this.LeadEngineer;
    if (sedanAssemblyInstructions.AirbagSafetyRating < MIN_SAFETY && sedanEngineer.IsCertifiedByFord)
    {
      // ...
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

呸。然后在所有这些工作之后，我们返回抽象类型表示。因此，即使调用者神奇地知道传入具体的输入类型，他们也必须神奇地知道转换输出类型(并祈祷我们返回了正确的类型):

```
SedanFactory sedanFactory = new SedanFactory
{
  AssemblyLine = new SedanAssemblyLine
  {
    SedanMakersUnionType = "Honda Sedan Workers of America",
    LeadEngineer = new SedanEngineer
    {
      Name = "Bob Jones",
      IsCertifiedByFord = true
    }
  }
};

SedanAssemblyLineIstructions sedanAssemblyLineIstructions = new SedanAssemblyLineIstructions
{
  MaxNumberOfUnitsToBuildInParallel = 2,
  AirbagSafetyRating = 4
};

Vehicle vehicle = sedanFactory.AssemblyLine.Build(sedanAssemblyLineIstructions);
Sedan sedan = (Sedan)vehicle;

Console.WriteLine($"Is convertible? {sedan.IsConvertible}. Tire size: {sedan.TireSize}"); 
```

Enter fullscreen mode Exit fullscreen mode

所以。很多。选角。我们能摆脱它吗？嗯，是的，但是不好看。下面是基类:

```
public abstract class Vehicle
{
  public void Move() { }
  public abstract int PassengerCapacity { get; }
  // ...
}

public abstract class VehicleEngineer
{
  public string Name { get; set; }
  // ...
}

public abstract class VehicleAssemblyLineInstructions
{
  public int MaxNumberOfUnitsToBuildInParallel { get; set; }
  // ...
}

public abstract class VehicleAssemblyLine<E, I, V>
 where E : Engineer
 where I : VehicleAssemblyLineInstructions
 where V : Vehicle
{
  public E LeadEngineer { get; set; }
  public abstract V Build(I instructions);
}

public abstract class VehicleFactory<L, E, I, V>
  where L : VehicleAssemblyLine<E, I, V>
  where E : Engineer
  where I : VehicleAssemblyLineInstructions
  where V : Vehicle
{
  public string FactoryName { get; set; }
  public L AssemblyLine { get; set; }
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是新的具体类:

```
public class Sedan : Vehicle
{
  public override int PassengerCapacity => 4;
  public bool IsConvertible { get; set; }
  public int TireSize { get; set; }
  // ...
}

public class SedanEngineer : VehicleEngineer
{
  public bool IsCertifiedByFord { get; set; }
  // ...
}

public class SedanAssemblyLineInstructions : VehicleAssemblyLineInstructions
{
  public int AirbagSafetyRating { get; set; }
  // ...
}

public class SedanAssemblyLine : VehicleAssemblyLine<SedanEngineer, SedanAssemblyLineInstructions, Sedan>
{
  public string SedanMakersUnionType { get; set; }
  public abstract Vehicle Build(SedanAssemblyLineInstructions instructions)
  {
    // Look ma, no casting!
    if (instructions.AirbagSafetyRating < MIN_SAFETY && LeadEngineer.IsCertifiedByFord)
    {
      // ...
    }
  }
}

public class SedanFactory : VehicleFactory<SedanAssemblyLine, SedanEngineer, SedanAssemblyLineInstructions, Sedan>
{
  public string SedanManufacturerName { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

这对我们来说有很多好处。所有方法参数、属性等都被编译器强类型化为通用约束。调用者不能传递给我们任何不是我们想要的类型的东西，我们也不用强制转换任何东西。呼叫者代码也是如此:

```
SedanFactory sedanFactory = new SedanFactory
{
  AssemblyLine = new SedanAssemblyLine
  {
    SedanMakersUnionType = "Honda Sedan Workers of America",
    LeadEngineer = new SedanEngineer
    {
      Name = "Bob Jones",
      IsCertifiedByFord = true
    }
  }
};

SedanAssemblyLineIstructions sedanAssemblyLineIstructions = new SedanAssemblyLineIstructions
{
  MaxNumberOfUnitsToBuildInParallel = 2,
  AirbagSafetyRating = 4
};

// The sedan factory's assembly line returns a sedan. Just like I want!
Sedan sedan = sedanFactory.AssemblyLine.Build(sedanAssemblyLineIstructions);
Console.WriteLine($"Is convertible? {sedan.IsConvertible}. Tire size: {sedan.TireSize}"); 
```

Enter fullscreen mode Exit fullscreen mode

对消费者来说更好。但是哇，那个带有 4 个泛型参数的`VehicleAssemblyLine`类让我恶心。此外，泛型参数使得它很难以抽象的方式处理。例如，假设您以前有一个方法，它接受一个任意的工厂并输出它的名字:

```
public void Print(VehicleFactory factory)
{
  Console.WriteLine($"Welcome to {factory.FactoryName}");
} 
```

Enter fullscreen mode Exit fullscreen mode

再也不可能了。现在有一个方法变得非常丑陋:

```
public void Print<L, E, I V>(VehicleFactory<L, E, I, V> factory)
  where L : VehicleAssemblyLine<E, I, V>
  where E : Engineer
  where I : VehicleAssemblyLineInstructions
  where V : Vehicle
{
  Console.WriteLine($"Welcome to {factory.FactoryName}");
} 
```

Enter fullscreen mode Exit fullscreen mode

还有一件事:假设您在 XML、JSON 或 YML 中序列化对象。假设您想要读取它们的集合，但是您不知道或者不关心这种情况下的具体类型——只是您想要读取公共属性。您从这些类中移除 abstract 关键字，以便解析器可以构造它们。如果没有泛型，你可以这样做:

```
VehicleFactory factory = JsonConvert.DeserializeObject<Factory>(payload);
Console.WriteLine($"Factory {factory.FactoryName}'s assembly line is run by {factory.AssemblyLine.LeadEngineer.Name}"); 
```

Enter fullscreen mode Exit fullscreen mode

但不会了。现在，事情变得真的，*真的*令人毛骨悚然:

```
VehicleFactory<VehicleAssemblyLine<Engineer, VehicleAssemblyLineInstructions, Vehicle>, Engineer, VehicleAssemblyLineInstructions, Vehicle> factory = JsonConvert.DeserializeObject<VehicleFactory<VehicleAssemblyLine<Engineer, VehicleAssemblyLineInstructions, Vehicle>, Engineer, VehicleAssemblyLineInstructions, Vehicle>>(payload);
Console.WriteLine($"Factory {factory.FactoryName}'s assembly line is run by {factory.AssemblyLine.LeadEngineer.Name}"); 
```

Enter fullscreen mode Exit fullscreen mode

即使你是那些在所有变量声明中使用`var`关键字的“酷孩子”之一，你仍然会为了声明一个类型而换行*。*

泛型方法还有其他问题，比如编译器似乎没有很好地处理泛型继承，但在我看来，最大的问题是，类声明中泛型的数量将随着您希望通过泛型类型声明公开的具体属性的数量而线性增长。虽然它开始时总是只有一两个属性，但它会一直增长。

但另一方面，增加泛型类型参数的另一种选择是增加您和调用者都必须执行的恼人而危险的强制转换的数量。

我只是找不到一个好的方法来做这件事。有没有其他我忘记的设计模式或实践可以解决这个问题？真的很感谢大家的评论。