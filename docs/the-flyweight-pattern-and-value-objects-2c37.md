# Flyweight 模式和值对象

> 原文：<https://dev.to/asarnaout/the-flyweight-pattern-and-value-objects-2c37>

作为软件工程师和建模人员，我们通常每天都会遇到不同的代码设计问题；这些难题中的大部分已经被解决，并且已经制定了最佳实践来定义开发者之间的合同，详细说明应该如何解决这些问题。开箱即用的解决方案通常会产生更多的复杂性和对规范的偏离，这就是为什么——在我看来——我相信对设计模式的深入了解是影响团队代码库质量的一个重要因素。然而，对这些模式的了解只是等式的一半，另一半是能够选择适当的情况来应用设计模式解决方案，并且同样能够发现模式何时被滥用或者何时根本没有意义。

一个有趣的结构模式是飞重(伽马等人，1995 年)。通常，开发人员会面临这样的情况:创建了太多相同的对象；管理这个过程将定义一个稳定的系统和一个不可用的系统之间的区别，这就是 Flyweight 的作用。

但首先，两个物体相同意味着什么？

让我们回顾一下模型中两类对象之间的重要区别:实体和值对象。

从概念上讲，每个人都是一个独立的“实体”，有自己的身份和连续性；一个人可以改变他们的名字、发色、性别和国籍，但他们的身份保持不变，这意味着具有完全相同属性/特征的两个人不能被认为是相同的。

[![](img/36a0c3130589fd514ea8230a90b29982.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qHZ2Nl7r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jkhpkjlfmy0a51tdvi5a.png)

另一方面，在我们可感知的世界中，有几个概念缺乏同一性，但它们仍然传达一些意义，一个著名的例子是一张一美元的钞票；我们并不真正关心我们拥有哪张一美元钞票，就像我们关心它的价值(价值对象)一样，因此我们通常认为所有的一美元钞票都是一样的。

当设计一个模型时，决定一个对象(表示领域范围内的一些概念)是否拥有或缺乏一个身份的过程不是一个无关紧要的问题，并且需要对手头的问题有所了解，并且这个决定很可能会因领域而异。

值对象比实体更自由，具有相同属性的两个值对象被认为是相同的，不像实体需要基于身份的比较机制。从这一点来看，可以肯定地说，值对象是可互换的，这意味着多个对象可以引用/共享一个值对象的实例，而不会导致一些概念上的困境。例如，一个不需要唯一地标识车队中每辆车的运输应用程序——意味着这个模型中的一辆车是一个值对象——可以安全地创建几十次运输，引用一辆车的一个实例，如果适用的话。

[![](img/4347a260b662f9b3a96bf6a92d9f4651.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XFW-4UBK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u4ljz0000w8evrd9btwd.png)

作为前面陈述的推论，值对象应该是不可变的；可交换性意味着对可交换对象的实例有副作用的操作将导致整个系统的连锁反应。因此，适用于值对象的命令应该没有副作用，并返回对象的新实例，而不是改变它。

从概念上讲，值对象的不变性是正确的，因为值对象将*总是*描述对宇宙中一些概念的观察。例如，颜色“红色”将总是描述具有一些可观察特征的独特颜色。将颜色“绿色”添加到“红色”不会改变颜色“红色”的可观察特征，而是会产生新的颜色“黄色”(这类似于对值对象的命令，该命令返回对象的新实例)。

[![](img/c5851007f4e550285e88c85f042ffa58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gXvJvh7z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s58f2ur6a998y2i1fga6.jpg)

所以回到最初的问题，系统需要管理大量相同对象的创建和存在。从逻辑上讲，这些对象应该是应用 Flyweight 模式重构的值对象；值对象的可交换性允许开发人员引入一个抽象(Flyweight Factory ),该抽象能够重用一组预先存在的对象(Flyweights ),以便获得对对象的引用，而值对象的不变性允许客户安全地对 Flyweights 发出命令，而不用担心这些命令是否会对系统产生副作用。

因此，让我们定义该模式的三个主要组成部分:

*   Flyweight:要在系统内交换的值对象。

*   Flyweight Factory:负责以有效的方式检索 Flyweight 的抽象。

*   客户。

假设我们正在处理一个被分配给不同运输路线的大量*区域*对象所组成的运输系统。为了应用 Flyweight 模式重构，我们首先将*区域*定义为一个不可变的值对象，它将充当 Flyweight。

```
public class Zone : IEquatable<Zone>
//An immutable flyweight...This represents a domain value object
{
    public string ZipCode { get; }

    public IReadOnlyCollection<string> AvailableCouriers { get; private set; }

    public Zone (string zipCode) 
    {
        ZipCode = zipCode;
        AvailableCouriers = new List<string> ();
    }

    public Zone (string zipCode, IEnumerable<string> couriers) : this (zipCode) 
    {
        AvailableCouriers = new List<string> (couriers.Distinct ());
    }

    public Zone RegisterCourier (string courier) 
    {
        var newCouriers = new List<string> (AvailableCouriers) 
        {
            courier
        };

        return new Zone (ZipCode, newCouriers);
    }

    public bool Equals (Zone other) 
    {
        if (other == default (Zone))
            return false;

        if (ReferenceEquals (this, other))
            return true;

        if (!string.Equals (ZipCode, other.ZipCode, StringComparison.OrdinalIgnoreCase))
            return false;

        if (AvailableCouriers.Count != other.AvailableCouriers.Count)
            return false;

        if (!AvailableCouriers.All (x => other.AvailableCouriers.Contains (x)))
            return false;

        return true;
    }
} 
```

然后我们引入充当 Flyweight 工厂的 ZoneFactory，在这种情况下，一个单独的独立工厂是合理的，因为管理 Flyweight 集合的责任不属于 Flyweight 对象。在这个实现中，我们使用一个字典来跟踪所有现有的区域轻量级。

```
public class ZoneFactory
{
    private readonly IDictionary<string, Zone> _zones = new Dictionary<string, Zone>();
    private readonly object _lock = new object();

    public Zone GetZone(string zipCode)
    {
        lock (_lock)
        {
            if (_zones.TryGetValue(zipCode, out var zone))
            {
                return zone;
            }

            zone = new Zone(zipCode);
            _zones.Add(zipCode, zone);
            return zone;
        }
    }
} 
```

最后，客户机可以自由调用 Flyweight 工厂并在 Flyweight 上发出命令。

```
public class ZoneClient
{
    static Random rnd = new Random();

    public IEnumerable<ShippingRoute> CreateShippingRoutes(IList<string> usZipCodes)
    {
        var routes = new List<ShippingRoute>();
        var zoneFactory = new ZoneFactory();

        for (var i = 0; i < 2000000; i++)
        {
            var destination = zoneFactory.GetZone(usZipCodes[rnd.Next(usZipCodes.Count)]);

            if(i % 100 == 0) //Issuing a command on the Value Object
                destination = destination.RegisterCourier("Fedex"); 

            routes.Add(new ShippingRoute(destination));
        }

        return routes;
    }
} 
```

更新#1:

值得注意的是，值对象不应该与值类型混淆。NET 结构)，实际上我更喜欢将值对象实现为不可变的类，而不是结构，但毕竟，这是一个实现细节，对值对象的概念意义没有任何贡献。