# 构建器模式

> 原文：<https://dev.to/franiglesias/builder-pattern-56ka>

过去几天，我一直在使用 Builder 图案，因此，请写一些关于该图案对于创建复杂对象的有用性的文章。

让我看看。在 PHP 中，我们可以使用□t0、∞创建或实例化对象吗？：

```
$objeto = new Class('Mi Objeto'); 
```

-当然可以。

但是，光着身子的`new`并不总是最方便的方式。在许多情况下，我们需要更好地控制我们创建对象的方式。

例如，我们需要的物件类型可能是由执行阶段取得的某些参数所决定，因此我们事先不知道要建立哪一个类别，而必须撰写一些程式码来决定:

```
switch ($type) {
    case 'customer':
        $relation = new Customer($someData);
        break;
    case 'provider':
        $relation = new Provider($someData);
        break;
    default:
        throw new InvalidArgumentException('Unsupported type');
} 
```

很明显，通常是将此代码封装在一个类中，从而生成一个名为 **Factory** 的模式(我们将在某个时候谈论它)。就像这样:

```
interface Relation
{
    public function data(): array;
}

class Customer implements Relation
{
    /** @var array */
    private $data;

    public function __construct(array $data)
    {
        $this->data = $data;
    }
    public function data(): array
    {
        return $this->data;
    }
}

class Provider implements Relation
{
    /** @var array */
    private $data;

    public function __construct(array $data)
    {
        $this->data = $data;
    }
    public function data(): array
    {
        return $this->data;
    }
}

class RelationFactory
{
    public function create($type, array $someData) : Relation
    {
        switch ($type) {
            case 'customer':
                return new Customer($someData);
            case 'provider':
                return new Provider($someData);
            default:
                throw new InvalidArgumentException('Unsupported type');
        }
    }
} 
```

这样，当我们需要实例化像`Relation`这样的物体时，我们会这样做:

```
$relationFactory = new RelationFactory();

//...

$relation = $relationFactory->create('customer', []); 
```

总之，**工厂**允许我们封装一个最终运行`new`的对象创建算法。

但我们在这里谈论的是另一种创作模式:Builder 模式。

## 对图案生成器的案例

一般而言，您可以使用编写模式来封装演算法，这些演算法通常是根据执行阶段才知道的参数来完成物件的实体化。

正如我们刚才看到的，因子通常允许我们根据提供给它们的某些数据请求创建对象类型。

但现在让我们来谈谈另一个问题。假定下面的类“`PostalAddress`”定义了一个值对象:

```
class PostalAddress {
    /** @var string */
    private $street;
    /** @var string */
    private $streetNumber;
    /** @var string */
    private $stairs;
    /** @var string */
    private $floor;
    /** @var string */
    private $door;
    /** @var string */
    private $postalCode;
    /** @var string */
    private $city;
    /** @var string */
    private $province;

    /**
     * PostalAddress constructor.
     * @param string $street
     * @param string $streetNumber
     * @param string $stairs
     * @param string $floor
     * @param string $door
     * @param string $postalCode
     * @param string $city
     * @param string $province
     */
    public function __construct(
        string $street,
        string $streetNumber,
        string $stairs,
        string $floor,
        string $door,
        string $postalCode,
        string $city,
        string $province
    ) {
        $this->street = $street;
        $this->streetNumber = $streetNumber;
        $this->stairs = $stairs;
        $this->floor = $floor;
        $this->door = $door;
        $this->postalCode = $postalCode;
        $this->city = $city;
        $this->province = $province;
    }

    /**
     * @return string
     */
    public function street(): string
    {
        return $this->street;
    }

    /**
     * @return string
     */
    public function streetNumber(): string
    {
        return $this->streetNumber;
    }

    /**
     * @return string
     */
    public function stairs(): string
    {
        return $this->stairs;
    }

    /**
     * @return string
     */
    public function floor(): string
    {
        return $this->floor;
    }

    /**
     * @return string
     */
    public function door(): string
    {
        return $this->door;
    }

    /**
     * @return string
     */
    public function postalCode(): string
    {
        return $this->postalCode;
    }

    /**
     * @return string
     */
    public function city(): string
    {
        return $this->city;
    }

    /**
     * @return string
     */
    public function province(): string
    {
        return $this->province;
    }

} 
```

并不是说它是一个特别复杂的物体，但它只有八个参数。使用`new`实例化其中一个是完全可能的，但这样做确实很不方便，而且很容易出错，因为不同元素的添加顺序必须不同。

我们还知道，有些数据是相互关联的。例如，街道和门号通常一起走，或邮政编码、城市和省。另一方面，可以有街道地址和数字地址，但不能有街道地址和数字地址。

身体要求我们做的第一件事就是创造`setters`，这样我们就能更有意义地构造物体。但是如果我们创造`setters`，我们就要从构造者中去除参数，甚至把它们全部删除。

∞天啊！但这将使我们面临更大的问题，我们将立即制造出我们永远无法确定其状态的不一致物体，并揭示其结构。

解决办法是一种模式。这个图案是 Builder。

## 为我们的对象建造构造器

构建器的想法是拥有一个用于构建对象的对象，这值得冗馀，由于某种原因，冗馀很难装配，首先收集所有必需的信息，然后返回构建的对象。

以`PostalAddress`为例，我们的问题是:

*   我们有八个参数，这样很容易踩脚，改变顺序等等。
*   一些参数往往在一起:例如街道和门号。
*   某些参数必须遵循某些规则:街道必须始终存在，但可能是没有端口号的地址。
*   楼板、门和楼梯是可选的，但通常是关联的。所以门没有地板就没有意义。
*   通常，地点、邮政编码和省一起去，不应该缺少其中任何一个。

除此之外:

*   一些参数可以从其他参数中获得。例如，从人口来看，如果考虑街道和门号，也可以获得省和邮政编码。

因此，一类`PostalAddressBuilder`应封装这些规则。我们希望能像这样使用它:

```
$builder = new PostalAddressBuilder();

$builder->withAddress($street, $number, $floor, $door);
$builder->atLocality($postalCode, $city, $province);

$address = $builder->build(); 
```

或者什么的：

```
$builder = new PostalAddressBuilder();

$builder->withApartmentAddress($street, $number, $stairs, $floor, $door);
$builder->atLocality($postalCode, $city, $province);

$address = $builder->build(); 
```

或者什么的：

```
$builder = new PostalAddressBuilder();

$builder->withHouseAddress($street, $number);
$builder->at($city);

$address = $builder->build(); 
```

也就是说，我们将有不同的更具语义的界面来构建我们的 postaladdress 对象，即使我们有部分数据，以便构建器能够获取、推断或提供适当的默认值。

## 循序渐进的构建器

原则上，构建器看起来有点像它所构建的对象。首先，它具有映射其特性的特性:

```
class PostalAddressBuilder
{
    /** @var string */
    private $street;
    /** @var string */
    private $streetNumber;
    /** @var string */
    private $stairs;
    /** @var string */
    private $floor;
    /** @var string */
    private $door;
    /** @var string */
    private $postalCode;
    /** @var string */
    private $city;
    /** @var string */
    private $province;
} 
```

`PostalAddressBuilder`这些属性中的一些可能有用的默认值可能会在构造函数中启动，也可能不会在构造函数中启动。

除此之外，它还可能有依赖性。想象一下，我们有一个‘t0’在某个地方，它从一个地方向我们提供信息。

```
class PostalAddressBuilder
{
    /** @var string */
    private $street;
    /** @var string */
    private $streetNumber;
    /** @var string */
    private $stairs;
    /** @var string */
    private $floor;
    /** @var string */
    private $door;
    /** @var string */
    private $postalCode;
    /** @var string */
    private $city;
    /** @var string */
    private $province;
    /** @var PostalCodeService */
    private $postalCodeService;

    public function __construct(PostalCodeService $postalCodeService)
    {
        $this->street = '';
        $this->streetNumber = '';
        $this->stairs = '';
        $this->floor = '';
        $this->door = '';
        $this->postalCode = '';
        $this->city = '';
        $this->province = '';
        $this->postalCodeService = $postalCodeService;
    }
} 
```

嗯，邮政地址生成器的主要功能是为我们提供正确装配的`PostalAddress`物品，所以我们需要你有一种方法(`build`这样做给我们。很明显，我们将使用构造函数的值创建此对象`PostalAddress`，从而满足我们的第一个要求:

```
class PostalAddressBuilder
{
    /** @var string */
    private $street;
    /** @var string */
    private $streetNumber;
    /** @var string */
    private $stairs;
    /** @var string */
    private $floor;
    /** @var string */
    private $door;
    /** @var string */
    private $postalCode;
    /** @var string */
    private $city;
    /** @var string */
    private $province;
    /** @var PostalCodeService */
    private $postalCodeService;

    public function __construct(PostalCodeService $postalCodeService)
    {
        $this->street = '';
        $this->streetNumber = '';
        $this->stairs = '';
        $this->floor = '';
        $this->door = '';
        $this->postalCode = '';
        $this->city = '';
        $this->province = '';
        $this->postalCodeService = $postalCodeService;
    }

    public function build(): PostalAddress
    {
        return new PostalAddress(
            $this->street,
            $this->streetNumber,
            $this->stairs,
            $this->floor,
            $this->door,
            $this->postalCode,
            $this->city,
            $this->province
        );
    }
} 
```

显然，目前的结果并非完全令人满意，如果我们调用它，`build`方法将返回一个具有其全部空值的`PostalAddress`对象。

```
$builder = new PostalAddressBuilder($postalCodeService);

$address = $builder->build(); 
```

我们需要各种方法，使我们能够输入这些价值观念。例如，要开始:

```
class PostalAddressBuilder
{
    /** @var string */
    private $street;
    /** @var string */
    private $streetNumber;
    /** @var string */
    private $stairs;
    /** @var string */
    private $floor;
    /** @var string */
    private $door;
    /** @var string */
    private $postalCode;
    /** @var string */
    private $city;
    /** @var string */
    private $province;
    /** @var PostalCodeService */
    private $postalCodeService;

    public function __construct(PostalCodeService $postalCodeService)
    {
        $this->street = '';
        $this->streetNumber = '';
        $this->stairs = '';
        $this->floor = '';
        $this->door = '';
        $this->postalCode = '';
        $this->city = '';
        $this->province = '';
        $this->postalCodeService = $postalCodeService;
    }

    public function build(): PostalAddress
    {
        return new PostalAddress(
            $this->street,
            $this->streetNumber,
            $this->stairs,
            $this->floor,
            $this->door,
            $this->postalCode,
            $this->city,
            $this->province
        );
    }

    public function withAddress(
        string $street,
        ?string $streetNumber,
        ?string $floor,
        ?string $door = null
    ): void {
        $this->street = $street;
        $this->streetNumber = $streetNumber;
        $this->floor = $floor;
        $this->door = $door;
    }

    public function atLocality(
        string $postalCode,
        string $city,
        string $province
    ): void {
        $this->postalCode = $postalCode;
        $this->city = $city;
        $this->province = $province;
    }
} 
```

这些方法的签名向我们表明哪些参数是一起的，哪些参数是强制性的或不强制性的。

事实上，我们可以制定任何我们认为有用的方法，来定义一个对我们的特定使用情形有用的构造此类物体的协议。

请注意，虽然我们的物品`setters`不一定要有`setters`才能保持不变，但我们的`PostalAddressBuilder`却只有`getter` ( `build`)能够准确返回构建的物品，其他方法一直是`setters`

我认为，通过查看代码，可以很清楚地了解构建器方法的工作原理，因此我不会包括所有可能的实现。无论如何，作为指导，我留下以下线索:

*   没有什么可以阻止我们为我们的对象所需的每个参数设置方法，尽管这些方法通常应该具有某种意义。
*   理想情况下，该对象应具有某种形式的自我验证，但我认为它也可以包括在构建器中，特别是当我们需要提供一些参数时，这些参数在某种程度上是相互依赖的。

## 联合国智能建筑公司

构建器可能具有可执行某些任务的服务部门。

在我们的示例中，postaladdressbuilder 可以通过简单地传递地址来构建有效的 postaladdress 对象，因为通过此数据和街道可以同时获取省/市和邮政编码，从而节省了用户提供该对象的时间，甚至避免了错误。

所以我要举个例子，再加上一个方法`at`:

```
class PostalAddressBuilder
{
    /** @var string */
    private $street;
    /** @var string */
    private $streetNumber;
    /** @var string */
    private $stairs;
    /** @var string */
    private $floor;
    /** @var string */
    private $door;
    /** @var string */
    private $postalCode;
    /** @var string */
    private $city;
    /** @var string */
    private $province;
    /** @var PostalCodeService */
    private $postalCodeService;

    public function __construct(PostalCodeService $postalCodeService)
    {
        $this->street = '';
        $this->streetNumber = '';
        $this->stairs = '';
        $this->floor = '';
        $this->door = '';
        $this->postalCode = '';
        $this->city = '';
        $this->province = '';
        $this->postalCodeService = $postalCodeService;
    }

    public function build(): PostalAddress
    {
        return new PostalAddress(
            $this->street,
            $this->streetNumber,
            $this->stairs,
            $this->floor,
            $this->door,
            $this->postalCode,
            $this->city,
            $this->province
        );
    }

    public function withAddress(
        string $street,
        ?string $streetNumber,
        ?string $floor,
        ?string $door = null
    ): void {
        $this->street = $street;
        $this->streetNumber = $streetNumber;
        $this->floor = $floor;
        $this->door = $door;
    }

    public function atLocality(
        string $postalCode,
        string $city,
        string $province
    ): void {
        $this->postalCode = $postalCode;
        $this->city = $city;
        $this->province = $province;
    }

    public function at(string $city): void
    {
        try {
            $postalCodeRequest = new PostalCodeRequest($city, $this->street, $this->streetNumber);
            $response = $this->postalCodeService->find($postalCodeRequest);
            $this->atLocality(
                $response->postalCode,
                $response->city,
                $response->province
            );
        } catch (Exception $exception) {
            throw new CityNotFoundException(sprintf('City %s could not be found', $city));
        }
    }
} 
```

采用`at`的方法，我们给它传递了一个位置，并利用`PostalCodeService`依存关系进行信息检索。这样，postaladdress 对象就不必加载仅在构建时需要的此类依赖关系。

## 流畅的界面

对于构建者来说，一个有趣但并非必需的附加条件是它们提供了一个流畅的界面。

流畅的介面是，方法会传回 builder 物件本身，因此我们可以串连呼叫，并组装相当优雅的表示式，传回所需的物件:

```
$builder = new PostalAddressBuilder();

$address = $builder
    ->withHouseAddress($street, $number)
    ->at($city)
    ->build(); 
```

此表示式不仅更紧凑，而且更能正确表示物件的建立流程，并以统一的方式呈现。

为此，我们必须使我们的方法`setter`返回构建器实例本身，并将返回类型更改为 self:

```
class PostalAddressBuilder
{
    /** @var string */
    private $street;
    /** @var string */
    private $streetNumber;
    /** @var string */
    private $stairs;
    /** @var string */
    private $floor;
    /** @var string */
    private $door;
    /** @var string */
    private $postalCode;
    /** @var string */
    private $city;
    /** @var string */
    private $province;
    /** @var PostalCodeService */
    private $postalCodeService;

    public function __construct(PostalCodeService $postalCodeService)
    {
        $this->street = '';
        $this->streetNumber = '';
        $this->stairs = '';
        $this->floor = '';
        $this->door = '';
        $this->postalCode = '';
        $this->city = '';
        $this->province = '';
        $this->postalCodeService = $postalCodeService;
    }

    public function build(): PostalAddress
    {
        return new PostalAddress(
            $this->street,
            $this->streetNumber,
            $this->stairs,
            $this->floor,
            $this->door,
            $this->postalCode,
            $this->city,
            $this->province
        );
    }

    public function withAddress(
        string $street,
        ?string $streetNumber,
        ?string $floor,
        ?string $door = null
    ): self {
        $this->street = $street;
        $this->streetNumber = $streetNumber;
        $this->floor = $floor;
        $this->door = $door;

        return $this;
    }

    public function atLocality(
        string $postalCode,
        string $city,
        string $province
    ): self {
        $this->postalCode = $postalCode;
        $this->city = $city;
        $this->province = $province;

        return $this;
    }

    public function at(string $city): self
    {
        try {
            $postalCodeRequest = new PostalCodeRequest($city, $this->street, $this->streetNumber);
            $response = $this->postalCodeService->find($postalCodeRequest);
            return $this->atLocality(
                $response->postalCode,
                $response->city,
                $response->province
            );
        } catch (Exception $exception) {
            throw new CityNotFoundException(sprintf('City %s could not be found', $city));
        }
    }
} 
```

## 能建吗？

我们缺少的一个因素是:当我们调用 build 方法时，我们可能没有所有必要的信息，因此我们可能需要一些东西来验证和验证这种情况。

一个可能是最好的选择是，明信片地址包括自己的验证，以确保无论具体的构造方法如何，通过构造函数或直接构造，我们总能有一致的对象`PostalAddress`。

这样，如果无法正确构造对象，则该对象将抛出异常。那样的话我们就能抓住她然后重新启动。

方法大致如下:

```
class PostalAddressBuilder
{
    /** @var string */
    private $street;
    /** @var string */
    private $streetNumber;
    /** @var string */
    private $stairs;
    /** @var string */
    private $floor;
    /** @var string */
    private $door;
    /** @var string */
    private $postalCode;
    /** @var string */
    private $city;
    /** @var string */
    private $province;
    /** @var PostalCodeService */
    private $postalCodeService;

    public function __construct(PostalCodeService $postalCodeService)
    {
        $this->street = '';
        $this->streetNumber = '';
        $this->stairs = '';
        $this->floor = '';
        $this->door = '';
        $this->postalCode = '';
        $this->city = '';
        $this->province = '';
        $this->postalCodeService = $postalCodeService;
    }

    public function build(): PostalAddress
    {
        try {
            return new PostalAddress(
                $this->street,
                $this->streetNumber,
                $this->stairs,
                $this->floor,
                $this->door,
                $this->postalCode,
                $this->city,
                $this->province
            );
        } catch (Exception $exception) {
            throw new InvalidArgumentException('It was impossible to create a PostalAddress', 1, $exception);
        }
    }

    public function withAddress(
        string $street,
        ?string $streetNumber,
        ?string $floor,
        ?string $door = null
    ): self {
        $this->street = $street;
        $this->streetNumber = $streetNumber;
        $this->floor = $floor;
        $this->door = $door;

        return $this;
    }

    public function atLocality(
        string $postalCode,
        string $city,
        string $province
    ): self {
        $this->postalCode = $postalCode;
        $this->city = $city;
        $this->province = $province;

        return $this;
    }

    public function at(string $city): self
    {
        try {
            $postalCodeRequest = new PostalCodeRequest($city, $this->street, $this->streetNumber);
            $response = $this->postalCodeService->find($postalCodeRequest);
            return $this->atLocality(
                $response->postalCode,
                $response->city,
                $response->province
            );
        } catch (Exception $exception) {
            throw new CityNotFoundException(sprintf('City %s could not be found', $city));
        }
    }
} 
```

## 再续前缘

使用 Builder 模式，我们可以通过定义一个方便的构造界面来构建复杂的对象，同时保护它们在整个过程中的一致性。

Builder 是许多情况下使用的模式，尤其是构造查询对象时，因为它允许我们定义某种语言。理论上的 QueryBuilder 就是一个很好的例子。

他也是建造双考的好盟友，我们以后再谈这件事。