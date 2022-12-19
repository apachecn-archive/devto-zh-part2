# 如何在 C#中使用抽象工厂设计模式

> 原文：<https://dev.to/gary_woodfine/how-to-use-the-abstract-factory-design-pattern-in-c-1gpa>

如何在 C# 中使用抽象工厂设计模式的帖子[最早出现在](https://garywoodfine.com/abstract-factory-design-pattern/) [Gary Woodfine](https://garywoodfine.com) 上。

抽象工厂模式比[工厂方法设计模式](https://garywoodfine.com/factory-method-design-pattern/)高一个抽象层次。使用抽象工厂模式定义了一个框架，该框架产生遵循通用模式的对象，并且在运行时，该工厂与任何具体工厂配对，以产生遵循定义的模式的对象。

抽象工厂模式是一个创造性的四人组(g of)设计模式，在他们的开创性著作[Design Patterns:Elements of Reusable Object-Oriented Software](https://amzn.to/2N22a2H)中定义，在该书中，他们为常见的设计问题提供了一系列简单明了的解决方案。

### 什么是抽象工厂模式

当您想要返回几个相关的对象类时，可以使用抽象工厂模式，每个对象类可以根据请求返回几个不同的对象。通常你可以使用抽象工厂模式，结合其他工厂模式，比如[简单工厂模式](https://garywoodfine.com/simple-factory-pattern/)和[工厂方法模式](https://garywoodfine.com/factory-method-design-pattern/)。

思考抽象工厂模式的最佳方式是，它是一个超级工厂，或者是工厂中的*工厂。通常，它是一个接口，负责创建相关对象的工厂，而无需显式指定派生类。
T3![Abstract Factory Pattern](../Images/8fd668b130c229f9bad2158d948cd60c.png)T5】*

抽象工厂模式的关键组件是:

**抽象工厂:**创建抽象对象的抽象容器。

**具体工厂:**实现一个抽象容器来创建具体对象。

**抽象对象:**用要创建的对象的属性定义的接口。

**具体对象:**参照相关抽象对象的实际对象。

**客户端:**使用工厂创建相关对象系列的客户端应用程序。

### C 中的抽象工厂示例

在我们的示例中，我们将继续我们的示例游戏应用程序，根据传递给我们的方法的一组用户需求来构建一辆汽车。这是我们在[工厂方法设计模式](https://garywoodfine.com/factory-method-design-pattern/)文章中开始创建的一个假想游戏。我们将*重构*这个应用程序，引入抽象工厂设计模式，并为用户提供一些他们可以提供的额外选择。

用户将能够规定轮子的数量，它们是否有发动机，它们是否将运载货物，以及它们是否将运载乘客。一旦提供了这些信息，应用程序将返回最适合他们用途的车辆类型。该游戏将是一个简单的控制台应用程序，它只是要求用户输入他们的选择，并以一个答案作为响应。

为了说明这一点，我将代码保持得相当简单。我们整个控制台的代码如下:

```
 static void Main(string[] args)
        {
            var requirements = new VehicleRequirements();

            Console.WriteLine( "To Build a Vehicle answer the following questions");

            Console.WriteLine("How many wheels do you have ");
            var wheels = Console.ReadLine();
            int wheelCount = 0;

            if (!int.TryParse(wheels, out wheelCount))
            {
                wheelCount= 1;
            }

            requirements.NumberOfWheels = wheelCount;

            Console.WriteLine("Do you have an engine ( Y/n )");
            var engine = Console.ReadLine();
            switch (engine)
            {
                case "Y":
                    requirements.Engine = true;
                    break;
                case "N":
                    requirements.Engine = false;
                    break;
                default:
                    requirements.Engine = false;
                    break;
            }

            Console.WriteLine("How many passengers will you be carrying ?  (1 - 10)");

            var passengers = Console.ReadLine();
            var passengerCount = 0;

            if (!int.TryParse(passengers, out passengerCount))
            {
                passengerCount = 1;
            }

            requirements.Passengers = passengerCount;

            Console.WriteLine("Will you be carrying cargo");

            var cargo = Console.ReadLine();
            switch (cargo)
            {
                case "Y":
                    requirements.Engine = true;
                    break;
                case "N":
                    requirements.Engine = false;
                    break;
                default:
                    requirements.Engine = false;
                    break;
            }

            var vehicle = GetVehicle(requirements);

           Console.WriteLine(vehicle.GetType().Name);
        } 
```

本节中的代码尽管在这一点上很混乱，很难阅读，(这在这个阶段是相当谨慎的，因为我们将在以后的文章中解决这个问题！)-无非是问用户一组问题，然后将答案添加到定义好的`VehicleRequirements`中，然后传递给调用`Abstract Factory`的方法，稍后我将对此进行解释。

`GetVehicle`方法将返回实现我们的`IVehicle`接口的类的实例。

```
 private static IVehicle GetVehicle(VehicleRequirements requirements)
        {
            var factory = new VehicleFactory();
            IVehicle vehicle;

            if (requirements.HasEngine)
            {
                return factory.MotorVehicleFactory().Create(requirements);
            }

           return factory
                .CycleFactory().Create(requirements);

        } 
```

这是我们真正开始看到抽象工厂的实现的方法——或者我们的工厂的工厂。

在这个虚构的示例中，我们的方法将检查我们想要制造的车辆类型是否有发动机，并根据该数据选择我们是想要制造电动车辆还是踏板动力车辆，然后重定向到适当的工厂来制造车辆。理想情况下，你可能会推迟选择抽象工厂本身，但我只想说明一点，使用抽象工厂类，我们仍然可以决定使用哪个工厂。

抽象类上定义的两个方法都将为我们提供一个实现`IVehicle`接口的对象。应用程序不控制对象的最终类型，但是它可以根据某些标准选择要构建对象的工厂。

我们的抽象工厂实际上是一个用两个方法定义的`abstract`类，这两个方法需要实现另外两个工厂。您会注意到这两个方法也只是`IVehicleFactory`的实现。

```
 public abstract class AbstractVehicleFactory
    {
        public abstract IVehicleFactory CycleFactory();
        public abstract IVehicleFactory MotorVehicleFactory();

    } 
```

这个类只不过是一个抽象类，根本没有实现代码。如果需要，我们可以有一些默认的实现代码，如果需要，可以覆盖它。然而，出于我的目的，我只是将它们作为存根。

该类支持两个不同工厂的实现，每个工厂负责返回不同的车辆类型。在我们的例子中，自行车或汽车。

我们的类抽象工厂类的实际实现可能如下所示。

```
 public class VehicleFactory : AbstractVehicleFactory
    {
        public override IVehicleFactory CycleFactory()
        {
           return new Cyclefactory(); 
        }

        public override IVehicleFactory MotorVehicleFactory()
        {
            return new MotorVehicleFactory();
        }
    } 
```

事实上，我们的 Vehicle Factory 类的实现可能被认为是糟糕的编码实践，只不过是**传递方法**，但我有意实现了这一点，以说明最终我们的方法更有可能调用另一个工厂。

> 传递方法是除了调用另一个方法之外几乎不做任何事情的方法，该方法的签名与调用方法的签名相似或相同。这通常意味着阶级之间没有明确的划分
> 
> - [约翰·奥特-软件设计哲学](https://garywoodfine.com/philosophy-of-software-design/)

如果我们看一下其中一个工厂的实现，即`CycleFactory`，你会注意到我实际上选择了利用[工厂方法模式](https://garywoodfine.com/factory-method-design-pattern/)来创建一个返回对象的实例。我选择这一点是为了强调另一点，作为开发人员，您可以互换使用这些模式，并与其他模式相一致。

```
 public class Cyclefactory : IVehicleFactory
    {
        public IVehicle Create(VehicleRequirements requirements)
        {
            switch (requirements.Passengers)
            {
                case 1:
                    if(requirements.NumberOfWheels == 1) return new Unicycle();
                    return new Bicycle();
                case 2:
                    return new Tandem();
                case 3:
                    return new Tricyle();
                case 4:
                    if (requirements.HasCargo) return new GoKart();
                    return new FamilyBike();
                default:
                    return new Bicycle();
            }
       }
    } 
```

尽管这是一个非常巧妙的例子，但它确实起到了说明和强调工厂的工厂概念的作用。

我试图尽可能地保持这个示例中的逻辑，但也试图说明这个工厂最终负责实例化传递回调用客户端的对象。强调这样一个事实，客户端不需要关心对象的创建，返回哪个对象的决策点由几个层分开。

我们现在的工厂方法，是自由地实现某种*规则引擎*来创建基于需求的车辆。

这使得通过分离关注点来提高可测试性成为可能。即使在这个简单的例子中，您也会注意到，客户端只需要关注已经输入的数据，即尽可能靠近数据源验证数据，然后它将数据传递给抽象工厂类，然后抽象工厂类将数据传播给所需的工厂以创建对象。

正是这个特性使得抽象工厂方法成为一种流行且强大的学习模式，为单元测试代码提供了更多的机会。将该模式与其他模式结合使用可以进一步增强代码的可测试性。

### 重构工厂

我们的抽象工厂的代码是工作的，但是说实话，这是相当不干净的代码，它实际上并没有提供我们从使用抽象工厂模式中可以得到的所有好处。出于几个原因，我前面提到过。

让我们稍微重构一下工厂模式。第一个是，我们可能会删除客户端应用程序的选项，使其不必决定使用哪个工厂，或者我们可以扩展该选项。

为了说明一个选项，让我们做一个完整的重构，整理一下代码。我的第一个选择是删除那些讨厌的**传递方法**它们真的没有提供一个清晰的抽象，事实上我们可以删除在我们的类中有两个工厂方法的需要，只提供一个。这将使我们的类更容易理解，并消除任何出错的机会。

在我们的例子中，我们只给客户提供一个选择`Factory`。我们新的重构抽象工厂类现在只有一个方法。

```
 public abstract class AbstractVehicleFactory
       {
           public abstract IVehicle Create();
       } 
```

至此，让我们继续完成工厂类实现的重构。

```
private readonly IVehicleFactory _factory;
        private readonly VehicleRequirements _requirements;

        public VehicleFactory(VehicleRequirements requirements)
        {
            _factory = requirements.HasEngine  ? (IVehicleFactory) new MotorVehicleFactory() : new Cyclefactory();
            _requirements = requirements;

        }
        public override IVehicle Create()
        {
           return _factory.Create(_requirements);
        } 
```

你会注意到这里，我们实际上使用了构造函数来传递需求，并使用数据来确定哪个工厂最适合创建我们选择的车辆。这将最小化客户使用错误工厂的风险。

clients `GetVehicle`方法的复杂性已经大大降低，他们现在不再需要关心检查数据和决定使用哪个工厂。他们所需要做的就是在构造函数中传递`VehicleRequirements`并调用`Create`方法，所有神奇的事情就会发生。

```
 private static IVehicle GetVehicle(VehicleRequirements requirements)
        {
            var factory = new VehicleFactory(requirements);
            return factory.Create();
        } 
```

我们在这里实现的是*抽象*的一个重要方面

> 抽象是一个实体的简化视图，它省略了不重要的细节。
> - [约翰·奥特-软件设计哲学](https://garywoodfine.com/philosophy-of-software-design/)

我们已经设法[降低复杂性](https://garywoodfine.com/philosophy-of-software-design#complexity)并保护我们的用户远离复杂性，为他们提供一个易于使用的界面。

> 当开发一个模块时，寻找机会给自己增加一点额外的痛苦，以减少用户的痛苦。
> 
> - [约翰·奥特-软件设计哲学](https://garywoodfine.com/philosophy-of-software-design/)

### 结论

抽象工厂模式使开发人员能够一般性地定义相关对象的系列，将这些对象的实际具体实现留给需要的人来实现。

抽象工厂模式在 C#中很常见。net Framework 的库使用它来提供扩展和定制标准组件的方法。