# 反腐败层模式

> 原文：<https://dev.to/asarnaout/the-anti-corruption-layer-pattern-pcd>

众所周知，与遗留系统集成是一个无趣的里程碑。糟糕的文档、缺乏支持、混乱的界面和许多潜伏的错误只是您在集成过程中可能面临的问题的一部分。然而，出于技术和/或政治原因，整合往往是绝对必要的。与遗留系统的集成给正在设计的系统带来了风险，因为遗留模型通常设计很差，因此您的新的、设计良好的模型很可能被遗留集成破坏。

# 整合策略:

需要使用遗留系统的开发人员面临四种选择:

1-抛弃遗留问题:拥有一个共同的用例并不总是集成的理由。开发人员应该通过仔细检查遗留系统的价值并权衡集成的成本来考虑这个选项。

2-符合遗留系统:当组织没有替换遗留系统的计划时，并且当遗留模型的质量足够可接受时，那么努力地符合遗留模型将消除所有的集成成本。在外围扩展被添加到已建立的、占主导地位的遗留系统的情况下，必须认真考虑一致性方法。这种选择被严重低估了，因为我们不可抗拒地迷恋于重新发明轮子，浪费时间和金钱来制定几乎没有商业价值的解决方案。

3-反腐败层:一种高度防御性的策略，将您的模型与遗留模型的腐败隔离开来。

4-替换遗产:最不利的选择。开发人员应该避免逐步淘汰遗留系统的大型复杂模型(特别是当这些模型属于系统的核心领域时)，同时处理新模型，因为这些雄心勃勃的尝试更容易受到不希望的结果的影响。

在尝试反腐败层解决方案之前，您应该仔细考虑选项 1 和 2；反腐败层可能会变得非常丑陋和复杂，此外，它们还会构成新的元素，添加到需要维护和支持的组织环境图中。在讨论反腐败层模式之前，让我们先简要讨论两个突出的设计模式:

## 面对:

外观是一种模式，用于为复杂的系统提供更简单的界面。外观应该遵循接口系统的模型，避免引入自己的新模型(即:它们应该使用相同的语言)。

想象一个有以下界面的旅行计划系统:

```
public interface ITripService
{
    void BookAirline();
    void ReserveHotelRoom();
    void ReserveTouristAttractionTickets();
} 
```

需要时，可通过以下外观提供更简单的界面，协调整个操作的执行:

```
public interface ITripServiceFacade
{
    void PlanItinerary();
} 
```

## 适配器:

[![A Charger Adapter](img/306b4898c254d5712b7352524ca33260.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X16fp-f8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/31jsqekdxozo1p4gf121.png)T3】

<center>A Wall Adapter</center>

适配器是一种模式，它通过提供客户机能够理解的接口，允许两个不兼容的抽象之间进行通信。允许适配器说客户机的语言，并使其适应另一个子系统。

假设一个乘车应用程序具有以下界面:

```
public interface ITransportationService
{
    void StartTrip(string country, Zipcode from, Zipcode to);
} 
```

然而，当发现在某些国家/地区，旅行应该基于出发地和目的地的精确坐标而不是邮政编码时，我们提供了一个适配器:

```
public interface ITransportationServiceAdapter
{
    void StartTrip(Coordinates from, Coordinates to);
} 
```

请注意，适配器使用客户机的语言，并负责向适配器发起相应的请求。

# 反腐层:

[![The Great Fire](img/a3f96512f39d8cfd3a1b2c19f590e4d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eHnjTp7F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mvi9162honjdj2js6njt.jpg)T3】

<center>The Great Fire engulfing the city of Rome</center>

反腐败层结合了一组外观、适配器和转换器，以将一个模型与它需要集成的其他模型隔离开来。在两个模型之间存在客户-供应商关系的情况下，反腐败层可以是单向的，否则可以是双向的。

为了说明这种模式，让我们首先考虑一个电子商务应用程序，它通过估计运送订单所需的包裹数量和尺寸来显示订单的运送成本，然后使用复杂的网络遍历算法来查找最便宜的运送路线。由此我们可以很快推断出两个截然不同的上下文:装运和包装，各有不同的功能和完全独立的模型。

原来，该组织已经拥有了一个已使用多年的传统包装服务，因为该组织在将战略转向电子商务之前，曾专门从事包装行业。当架构师仔细检查遗留打包服务时，他们意识到与遗留的集成是不可避免的，因为服务是高度复杂的(表明大量知识已经被消化到遗留中),因此放弃遗留或尝试大规模替换是笨拙的解决方案。更仔细的检查还排除了因循守旧的方法，因为遗留模型非常过时，并且做出了不再适合当前业务模型的假设，因此使运输环境符合遗留包装模型会导致运输方面的高腐败率。

不幸的是，打包服务太老了，没有文档记录，并且因为许多错误而臭名昭著，这些错误迫使系统中的其他组件采取了一些临时的解决方法，此外，编写服务的原始开发人员已经离开了组织，留下了完全独立的运输团队。在这一点上，架构师决定将运输上下文与包装上下文集成，并使用反腐败层将运输模型与遗留系统隔离开来。

让我们更仔细地看一下传统包装模型，以突出模型的弱点以及它们如何破坏运输模型:

```
namespace Warehouse.Packaging 
{
    public class Container 
    { 
        //Irrelevant details
    }

    public class Package 
    { 
        public double LengthInInches { get; }
        public double WidthInInches { get; }
        public double HeightInInches { get; }
    }

    public class Item 
    {
        public double LengthInInches { get; }
        public double WidthInInches { get; }
        public double HeightInInches { get; }
    }

    public class Warehouse 
    {
        public string Code { get; }
        public string ZipCode { get; }
        public IEnumerable<Container> AvailableContainers { get; }
        //Further details of a messy model
    }

    public interface ILegacyPackagingService 
    {
        bool ValidateItemDimensions (IEnumerable<Item> items);
        IEnumerable<Package> PackageItems (IEnumerable<Item> items, Warehouse warehouse);
        IEnumerable<Package> OptimizePackaging (IEnumerable<Package> packages);
    }
} 
```

上述领域模型中存在几个问题:

1-`ILegacyPackagingService`提供了一个复杂的接口，其中验证、打包和优化作为三个独立的 API 提供-这可能是有原因的-然而，这并不符合运输模型的需求，因为从运输的角度来看，所有这些操作都被视为单个操作。

2-`Item`和`Package`型号使用英制测量系统，这与在所有新型号中采用公制的新组织范围政策相冲突。

3-打包模块中的仓库模型已经非常过时，不再反映企业如何对自己的仓库建模。

4 .`ILegacyPackagingService`中定义的`PackageItems` API 引入了打包逻辑和`Warehouse`模型之间的耦合——因为打包以前被视为只能在组织的一个指定仓库中进行的操作。然而，当前的运输模式不能支持这样的假设，因为它允许产品直接从外部供应商的存储设施运输。

显然，反腐败层有很多工作要做。允许上面提到的任何问题泄漏到运输模型中会产生一个带有遗留系统所有弱点的损坏的模型。为了缓解第一个问题，我们在反腐败层中引入了一个外观:

```
namespace Warehouse.Packaging.AntiCorruption.Shipping 
{
    public interface IPackagingServiceFacade 
    {
        IEnumerable<Warehouse.Packaging.Package> PackageItems (IEnumerable<Item> items, Warehouse warehouse);
    }

    public class PackagingServiceFacade : IPackagingServiceFacade 
    {
        private readonly ILegacyPackagingService _packagingService;

        public PackagingServiceFacade (ILegacyPackagingService packagingService) 
        {
            _packagingService = packagingService;
        }

        public IEnumerable<Warehouse.Packaging.Package> PackageItems (IEnumerable<Item> items, Warehouse warehouse) 
        {
            if (_packagingService.ValidateItemDimensions (items)) 
            {
                var packages = _packagingService.PackageItems (items, warehouse);
                var optimizedPackages = _packagingService.OptimizePackaging (packages);
                return optimizedPackages;
            }

            return Enumerable.Empty<Package> ();
        }
    }
} 
```

请注意，facade 使用与打包服务相同的模型，没有添加模型元素，并且保留了语言。外观只是为模型提供了一个更友好的界面。外观甚至放在包含打包上下文的`Warehouse.Packaging`模块/名称空间下(反腐败层可以跨多个模块)。

为了解决第二个问题，反讹误层将定义一个 translator 对象以在两个上下文中使用的不同模型之间进行映射，在下面的示例中，我们使用 AutoMapper 以简单的方式说明映射操作:

```
namespace Logistics.Shipping.AntiCorruption.Packaging
{
    public class PackagerProfile : Profile
    {
        private const double InchesCmConversionConst = 0.39370;

        public PackagerProfile()
        {
            CreateMap<Product, Item>()
                .ForMember(dst => dst.LengthInInches, opt => opt.MapFrom(src => src.LengthInCm * InchesCmConversionConst))
                .ForMember(dst => dst.WidthInInches, opt => opt.MapFrom(src => src.WidthInCm * InchesCmConversionConst))
                .ForMember(dst => dst.HeightInInches, opt => opt.MapFrom(src => src.HeightInCm * InchesCmConversionConst));

            CreateMap<Warehouse.Packaging.Package, Logistics.Shipping.Package>()
                .ForMember(dst => dst.LengthInCm, opt => opt.MapFrom(src => src.LengthInInches / InchesCmConversionConst))
                .ForMember(dst => dst.WidthInCm, opt => opt.MapFrom(src => src.WidthInInches / InchesCmConversionConst))
                .ForMember(dst => dst.HeightInCm, opt => opt.MapFrom(src => src.HeightInInches / InchesCmConversionConst));
        }
    }
} 
```

翻译器可以用来来回映射不兼容的模型。

为了处理第三个和第四个问题，我们向反腐败层引入了一个适配器，该适配器为运输服务提供了一个更加兼容的接口，该接口与`Warehouse`模型松散耦合，而是使用`Container`值对象来表示打包功能:

```
namespace Logistics.Shipping.AntiCorruption.Packaging
{
    public interface IPackagingService 
    {
        IEnumerable<Logistics.Shipping.Package> PackageItems (IEnumerable<Product> items, IEnumerable<Container> containers);
    }

    public class PackagingServiceAdapter : IPackagingService 
    {
        private readonly IPackagingServiceFacade _packagingServiceFacade;
        private readonly IMapper _mapper;

        public PackagingServiceAdapter (IPackagingServiceFacade packagingServiceFacade, IMapper mapper) 
        {
            _packagingServiceFacade = packagingServiceFacade;
            _mapper = mapper;
        }

        public IEnumerable<Logistics.Shipping.Package> PackageItems (IEnumerable<Product> products, IEnumerable<Container> containers) 
        {
            Warehouse warehouse; //Logic to initialize a transient dummy warehouse
            var items = _mapper.Map<IEnumerable<Item>> (products); //Using the translator to map the Product model to an Item
            var packages = _packagingServiceFacade.PackageItems (items, warehouse); //Calling the Façade 
            return _mapper.Map<IEnumerable<Logistics.Shipping.Package>> (packages); //Mapping the Packager's result to the Package model defined in the Shipping context
        }
    }
} 
```

其结果是一个与传统打包服务集成的运输模型，而不会被传统打包服务破坏:

```
namespace Logistics.Shipping 
{
    public class Zone 
    { 
    }

    public class Product 
    {
        public double LengthInCm { get; }
        public double WidthInCm { get; }
        public double HeightInCm { get; }
    }

    public class Package 
    {
        public double LengthInCm { get; }
        public double WidthInCm { get; }
        public double HeightInCm { get; }
    }

    public class Courier 
    {
        private IPackagingService _packagingService;

        public double GetQuote (IEnumerable<Product> items, Zone source, Zone destination) 
        {
            var packagingCapabilities = _capabilitiesService.GetPackagingCapabilities (source);
            var packages = _packagingService.PackageItems (items, packagingCapabilities.Containers);
            //Shipping specific logic goes here
        }
    }
} 
```

领域模型的大致图如下:

[![Domain Model](img/837c211e4c11c4727331b468c9f008d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NXNXN_5b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r4fjbtv8kxo9vb8fkls9.png)

为了简单起见，省略了关于抗腐蚀层的细节。

# 结论

[![Great Wall of China](img/5b0329cc1b90e0c251595acc3cb6def4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gzo_9sdv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mtr4tfi06tyj6czmbyb9.jpg)T3】

<center>The Great Wall of China</center>

开发反腐败层是一个耗时的过程，需要大量的分析和开发工作。架构师不应该立即采用这种解决方案，而是考虑其他简单的替代方案，然而，当没有其他选择时，反腐败层——就像包围堡垒的墙一样——将保护您的模型免受外部影响，并允许您孤立地工作。