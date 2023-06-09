# 如何在 C#中使用工厂方法设计模式

> 原文：<https://dev.to/gary_woodfine/how-to-use-factory-method-design-pattern-in-c-3ia3>

# 工厂方法设计模式介绍

工厂概念可能是最常见的设计模式，在面向对象编程中反复出现。

在[中，你会发现无数次引用和使用工厂模式。net 核心基础库](https://github.com/dotnet/corefx)和整个。net 框架源代码，最值得注意的并且可能是最常用的工厂之一可以在`System.Data.Common`名称空间和`DbProviderFactories`中找到。

工厂方法设计模式属于[四人帮(g of)设计模式](https://amzn.to/2RxkPTm)的创造性设计模式，最常用于创建对象。

在这篇文章中，我们将探索 C#中的工厂方法设计模式，这篇文章摘自[如何在 C#中使用工厂方法设计模式](https://garywoodfine.com/factory-method-design-pattern/)，它最早出现在 [Gary Woodfine](https://garywoodfine.com) 上。

## 什么是工厂方法模式

工厂方法模式是对一个概念的巧妙而微妙的扩展，这个概念是一个类充当一个交通警察，决定一个层次结构的哪个子类将被实例化。

在工厂模式中，开发人员在不公开创建逻辑的情况下创建对象。接口用于创建对象，但让子类决定实例化哪个类。开发人员可以通过编程来完成，而不是手动定义每个对象。

简而言之，工厂是一个不使用构造函数就能创建对象的对象。

该模式实际上没有一个决策点，在这个决策点上，一个子类被直接选择而不是另一个类。相反，开发实现这种模式的程序通常定义一个创建对象的抽象类，但让每个子类决定创建哪个对象。

## 何时使用工厂方法

在许多情况下，使用工厂方法开发应用程序是合适的。这些情况包括:

*   一个类不能预测它必须创建哪些类对象
*   一个类使用它的子类来指定它创建的对象
*   您需要本地化创建哪个类的知识

让基类知道它们的派生类型的实现细节通常被认为是一个坏主意。正是在这种情况下，您应该使用抽象工厂模式。

典型的情况可能是当构造函数需要返回它所在类型的实例时，工厂方法能够返回许多不同类型的对象，所有这些对象都属于同一继承层次结构。

## 如何实现工厂模式

我们将开发一种工厂方法，根据所需的车轮数量来创建车辆

[![factory pattern](img/6dc2bf877ea5f4bf8d2059e4f7d1670e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XbgSmVJJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://garywoodfine.com/wp-content/uploads/2018/10/factory-pattern.jpg)

一辆车可以由任意数量的轮子组成，例如，在游戏中，当一个角色需要建造一辆车时，他们有固定数量的轮子。

如果我们将车轮数量传递给我们的工厂方法，它将构建车辆并返回。

我们将保持这个例子的简单，这样我们就不会迷失在细节中，所以我们将定义一个简单的接口`IVEHICLE`，它只不过是一个空接口。

我们还将定义 4 个将继承`IVehicle`接口的类。独轮车、摩托车、汽车和卡车

```
 public interface IVehicle
 {

 }
 public class Unicycle : IVehicle
 {

 }
 public class Car : IVehicle
 {

 }
 public class Motorbike : IVehicle
 {

 }
 public class Truck : IVehicle
 {

 } 
```

我们现在可以用 build 方法创建一个`VechicleFactory`类，根据提供的车轮数量创建一辆车。

```
 public static class VehicleFactory
    {
        public static IVehicle Build(int numberOfWheels)
        {
            switch (numberOfWheels)
            {
                case 1:
                    return new UniCycle();
                case 2:
                case 3:
                    return new Motorbike();
                case 4:
                    return new Car();
                default :
                    return new Truck();

            }
        }
    } 
```

我们现在可以开发我们的小游戏，在这个例子中，它将是一个控制台应用程序，我们将要求用户输入一些车轮，然后我们将告诉他们他们制造了什么类型的车辆。

```
class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Enter a number of wheels between 1 and 12 to build a vehicle and press enter");

            var wheels = Console.ReadLine();
            var vehicle = VehicleFactory.Build(Convert.ToInt32(wheels));
            Console.WriteLine($" You built a {vehicle.GetType().Name}");
            Console.Read();
        }
    } 
```

如果您启动控制台应用程序，您将被要求输入一个介于 1 和 12 之间的数字，然后将根据提供的车轮数量创建一辆汽车。

这显然是一个非常简单的工厂方法的例子，只是为了说明如何实际使用它们。

如果你正在寻找一个更复杂的工厂方法模式的实现，看看我的 Threenine 的源代码。Map 应用程序，更具体地说，我巧妙地将这个类命名为 [MapConfigurationFactory](https://github.com/threenine/Threenine.Map/blob/master/src/MapConfigurationFactory.cs) 。

您会注意到，这个类基本上是应用程序的主脑，负责构建和加载应用程序中定义的所有映射配置。

它定义了许多入口点，但是我一直使用的主要方法是`Scan`方法，它最终是抽象工厂方法。

该库本身只包含 3 个接口`ICustomMap, IMapFrom, IMapTo`，这使得开发人员能够实现各种映射逻辑，并将其与 POCO 或与之相关的实体类相关联。关于它如何工作的详细解释[请查看文档](https://threeninemap.readthedocs.io/en/latest/MapConfigurationFactory.html)。

简而言之，`MapConfigurationFactory`所做的是，它使用反射来遍历包含在一个程序集中的所有库，并检索所有已经用任何接口标记的类，并将映射加载到[自动映射器——一个基于约定的对象-对象映射器](https://automapper.org/)。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Reflection;
using AutoMapper;

namespace Threenine.Map
{
    public class MapConfigurationFactory
    {
       public static void Scan<TType>(Func<AssemblyName, bool> assemblyFilter = null)
        {
            var target = typeof(TType).Assembly;

            bool LoadAllFilter(AssemblyName x) => true;

            var assembliesToLoad = target.GetReferencedAssemblies()
                .Where(assemblyFilter ?? LoadAllFilter)
                .Select(Assembly.Load)
                .ToList();

            assembliesToLoad.Add(target);

            LoadMapsFromAssemblies(assembliesToLoad.ToArray());
        }

        public static void LoadMapsFromAssemblies(params Assembly[] assemblies)
        {
            var types = assemblies.SelectMany(a => a.GetExportedTypes()).ToArray();
            LoadAllMappings(types);
        }

        public static void LoadAllMappings(IList<Type> types)
        {
            Mapper.Initialize(
                cfg =>
                {
                    LoadStandardMappings(cfg, types);
                    LoadCustomMappings(cfg, types);
                });
        }

        public static void LoadCustomMappings(IMapperConfigurationExpression config, IList<Type> types)
        {
            var instancesToMap = (from t in types
                from i in t.GetInterfaces()
                where typeof(ICustomMap).IsAssignableFrom(t) &&
                      !t.IsAbstract &&
                      !t.IsInterface
                select (ICustomMap) Activator.CreateInstance(t)).ToArray();

            foreach (var map in instancesToMap)
            {
                map.CustomMap(config);
            }
        }

        public static void LoadStandardMappings(IMapperConfigurationExpression config, IList<Type> types)
        {
            var mapsFrom = (from t in types
                from i in t.GetInterfaces()
                where i.IsGenericType && i.GetGenericTypeDefinition() == typeof(IMapFrom<>) &&
                      !t.IsAbstract &&
                      !t.IsInterface
                select new
                {
                    Source = i.GetGenericArguments()[0],
                    Destination = t
                }).ToArray();

            foreach (var map in mapsFrom)
            {
                config.CreateMap(map.Source, map.Destination);
            }

            var mapsTo = (from t in types
                from i in t.GetInterfaces()
                where i.IsGenericType && i.GetGenericTypeDefinition() == typeof(IMapTo<>) &&
                      !t.IsAbstract &&
                      !t.IsInterface
                select new
                {
                    Source = i.GetGenericArguments()[0],
                    Destination = t
                }).ToArray();

            foreach (var map in mapsTo)
            {
                config.CreateMap(map.Source, map.Destination);
            }
        }
    }
} 
```

### 结论

在我看来，工厂方法模式是软件开发中需要理解的最重要的模式之一。这是您最有可能实现的一种模式。工厂设计模式用于基于另一种数据类型实例化对象。工厂通常用于减少代码膨胀，并使修改需要创建的对象变得更容易。

如何在 C# 中使用工厂方法设计模式的帖子[最早出现在](https://garywoodfine.com/factory-method-design-pattern/) [Gary Woodfine](https://garywoodfine.com) 上。