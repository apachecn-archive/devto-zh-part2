# 用 TypeScript 和 InversifyJS 在 Node.js 中实现 SOLID 和 onion 架构

> 原文：<https://dev.to/remojansen/implementing-the-onion-architecture-in-nodejs-with-typescript-and-inversifyjs-10ad>

在本文中，我们将描述一种被称为洋葱架构的架构。洋葱架构是一个软件应用程序架构，它遵循坚实的原则。它广泛使用了依赖注入原则，并且受到了领域驱动设计(DDD)原则和一些函数式编程原则的深刻影响。

# 先决条件

下面的部分描述了一些软件设计原则和设计模式，为了能够理解洋葱架构，我们必须学习这些原则和模式。

## 关注点分离(SoC)原则

关注点是软件功能的不同方面。例如，软件的“业务逻辑”是一个关注点，而用户使用这个逻辑的界面是另一个关注点。

关注点的分离就是保持每个关注点的代码是分离的。更改接口应该不需要更改业务逻辑代码，反之亦然。

## 坚实的原则

SOLID 是一个缩写词，代表以下五个原则:

[![](img/92c5a0f5b839d7ac26daba2c46d1547a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--To-OoIj2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/awux83emfqr6x9dzfdss.png)

### 单一责任原则

> 一个类应该只有一个责任

破解应用最有效的方法就是创建神类。

> 神级是知道的太多或者做的太多的级。上帝对象是反模式的一个例子。

神类跟踪大量信息，有几个责任。一个代码更改很可能会影响到类的其他部分，从而间接影响到使用它的所有其他类。这反过来又导致了更大的维护混乱，因为除了添加新功能之外，没有人敢做任何改变。

下面的示例是一个 TypeScript 类，它定义了一个人；本课程不应包括电子邮件验证，因为这与人的行为无关:

```
class Person {
    public name : string;
    public surname : string;
    public email : string;
    constructor(name : string, surname : string, email : string){
        this.surname = surname;
        this.name = name;
        if(this.validateEmail(email)) {
          this.email = email;
        }
        else {
            throw new Error("Invalid email!");
        }
    }
    validateEmail(email : string) {
        var re = /^([\w-]+(?:\.[\w-]+)*)@((?:[\w-]+\.)*\w[\w-]{0,66})\.([a-z]{2,6}(?:\.[a-z]{2})?)$/i;
        return re.test(email);
    }
    greet() {
        alert("Hi!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过从 Person 类中移除电子邮件验证的职责并创建一个新的电子邮件类来改进上面的类:

```
class Email {
    public email : string;
    constructor(email : string){
        if(this.validateEmail(email)) {
          this.email = email;
        }
        else {
            throw new Error("Invalid email!");
        }        
    }
    validateEmail(email : string) {
        var re = /^([\w-]+(?:\.[\w-]+)*)@((?:[\w-]+\.)*\w[\w-]{0,66})\.([a-z]{2,6}(?:\.[a-z]{2})?)$/i;
        return re.test(email);
    }
}

class Person {
    public name : string;
    public surname : string;
    public email : Email;
    constructor(name : string, surname : string, email : Email){
        this.email = email;
        this.name = name;
        this.surname = surname;
    }
    greet() {
        alert("Hi!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

确保一个类有一个单一的职责，这使得默认情况下更容易看到它做什么，以及如何扩展/改进它。

### 开/关原理

> 软件实体应该对扩展开放，但对修改关闭。

下面的代码片段是一段不遵守打开/关闭原则的代码的例子:

```
class Rectangle {
    public width: number;
    public height: number;
}

class Circle {
    public radius: number;
}

function getArea(shapes: (Rectangle|Circle)[]) {
    return shapes.reduce(
        (previous, current) => {
            if (current instanceof Rectangle) {
                return current.width * current.height;
            } else if (current instanceof Circle) {
                return current.radius * current.radius * Math.PI;
            } else {
                throw new Error("Unknown shape!")
            }
        },
        0
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

前面的代码片段允许我们计算两个形状(矩形和圆形)的面积。如果我们试图添加对一种新形状的支持，我们将扩展我们的程序。我们当然可以添加对新形状的支持(我们的应用程序对扩展是开放的)，问题是这样做我们将需要修改 getArea 函数，这意味着我们的应用程序也对修改开放。

这个问题的解决方案是利用面向对象编程中的多态性，如下面的代码片段所示:

```
interface Shape {
    area(): number;
}

class Rectangle implements Shape {

    public width: number;
    public height: number;

    public area() {
        return this.width * this.height;
    }
}

class Circle implements Shape {

    public radius: number;

    public area() {
        return this.radius * this.radius * Math.PI;
    }
}

function getArea(shapes: Shape[]) {
    return shapes.reduce(
        (previous, current) => previous + current.area(),
        0
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

新的解决方案允许我们添加对新形状的支持(开放用于扩展)，而无需修改现有的源代码(关闭用于修改)。

### 利斯科夫替代原理

> 程序中的对象应该可以用其子类型的实例替换，而不会改变程序的正确性。

Liskov 替换原则也鼓励我们在面向对象编程中利用多态性。在前面的例子中:

```
function getArea(shapes: Shape[]) {
    return shapes.reduce(
        (previous, current) => previous + current.area(),
        0
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用接口`Shape`来确保我们的程序对扩展开放，但对修改关闭。利斯科夫替换原则告诉我们，我们应该能够将任何子类型的`Shape`传递给`getArea`函数，而不改变程序的正确性。在像 TypeScript 这样的静态编程语言中，编译器会为我们检查子类型的正确实现(例如，如果`Shape`的实现缺少`area`方法，我们会得到一个编译错误)。这意味着我们不需要做任何手工工作来确保我们的应用程序遵守 Liskov 替换原则。

### 界面偏析原理

> 许多特定于客户端的接口比一个通用接口要好。

接口分离原则帮助我们防止违反单一责任原则和关注点分离原则。假设你有两个领域实体:矩形和圆形。您已经在您的域服务中使用这些实体来计算它们的面积，并且工作得非常好，但是现在您需要能够在您的一个基础设施层中序列化它们。我们可以通过向形状接口添加一个额外的方法来解决这个问题:

```
interface Shape {
    area(): number;
    serialize(): string;
}

class Rectangle implements Shape {

    public width: number;
    public height: number;

    public area() {
        return this.width * this.height;
    }

    public serialize() {
        return JSON.stringify(this);
    }
}

class Circle implements  Shape {

    public radius: number;

    public area() {
        return this.radius * this.radius * Math.PI;
    }

    public serialize() {
        return JSON.stringify(this);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们的域层需要 area 方法(来自`Shape`接口)，但是它不需要知道任何关于序列化的东西:

```
function getArea(shapes: Shape[]) {
    return shapes.reduce(
        (previous, current) => previous + current.area(),
        0
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的基础设施层需要 serialize 方法(来自`Shape`接口)，但是它不需要知道任何关于区域:
的信息

```
// ...
return rectangle.serialize(); 
```

Enter fullscreen mode Exit fullscreen mode

问题是将名为 serialize 的方法添加到形状接口违反了 SoC 原则和单一责任原则。形状是一个业务问题，可序列化是一个基础设施问题。我们不应该在同一个界面中混合这两种关注。

接口分离原则告诉我们，许多特定于客户端的接口比一个通用接口更好，这意味着我们应该分离我们的接口:

```
interface RectangleInterface {
    width: number;
    height: number;
}

interface CircleInterface {
    radius: number;
}

interface Shape {
    area(): number;
}

interface Serializable {
    serialize(): string;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用新的接口，我们以一种完全独立于基础设施的方式实现了我们的领域层，比如序列化:

```
class Rectangle implements RectangleInterface, Shape {

    public width: number;
    public height: number;

    public area() {
        return this.width * this.height;
    }
}

class Circle implements CircleInterface, Shape {

    public radius: number;

    public area() {
        return this.radius * this.radius * Math.PI;
    }
}

function getArea(shapes: Shape[]) {
    return shapes.reduce(
        (previous, current) => previous + current.area(),
        0
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

在基础设施层，我们可以使用一组新的实体来处理序列化:

```
class RectangleDTO implements RectangleInterface, Serializable {

    public width: number;
    public height: number;

    public serialize() {
        return JSON.stringify(this);
    }
}

class CircleDTO implements CircleInterface, Serializable {

    public radius: number;

    public serialize() {
        return JSON.stringify(this);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用多个接口而不是一个通用接口帮助我们防止了违反 SoC 原则(业务层对序列化一无所知)和单一责任原则(我们没有既知道序列化又知道面积计算的类神类)。

我们可以认为`RectangleDTO`和矩形`Rectangle`几乎是相同的，它们违反了“不要重复自己”(DRY)原则。我认为事实并非如此，因为虽然它们看起来一样，但它们涉及两个不同的问题。当两段代码看起来很像时，并不总是意味着它们是同一个东西。

此外，即使它们违反了干燥原则，我们也必须在违反干燥原则或固体原则之间做出选择。我认为干燥的原则不如坚实的原则重要，因此，在这种特殊情况下，我要“重复我自己”。

### 依存倒置原则

> 一个人应该依靠抽象，而不是具体。

依赖倒置原则告诉我们，我们应该总是试图依赖接口，而不是类。值得一提的是，依赖倒置和依赖注入不是一回事。

不幸的是，依赖倒置原则用实线中的 D 表示。它总是最后解释的原理，但它是固体中最重要的原理。没有依赖倒置原则，大多数其他坚实的原则是不可能的。如果我们回过头来重新审视之前解释过的所有原则，我们会意识到接口的使用是每个原则中最基本的元素之一:

*   依赖于遵循接口分离原则的接口允许我们将一个层与另一个层的实现细节(SoC 原则)隔离开来，并帮助我们防止违反单一责任原则。

*   依赖一个接口也允许我们用另一个实现替换一个实现(Liskov 替换原理)。

*   依赖接口使我们能够编写可扩展但不可修改的应用程序(开/关原则)。

在不支持接口的编程语言或不支持多态性的编程范式中实现坚实的原则是非常不自然的。例如，在 JavaScript ES5 甚至 ES6 中实现坚实的原则感觉非常不自然。然而，在 TypeScript 中，它感觉是尽可能自然的。

## 模型-视图-控制器(MVC)设计模式

MVC 设计模式将应用程序分成三个主要部分:模型、视图和控制器。

[![](img/cf79c64a23e35d5ef8031db8dab4dcd7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---4g7weaO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yl84y5ypxi0i1pfzc7hb.png)

### 型号

模型对象是应用程序中实现应用程序数据域逻辑的部分。通常，模型对象在数据库中检索和存储模型状态。例如，产品对象可能从数据库中检索信息，对其进行操作，然后将更新的信息写回 SQL Server 数据库中的产品表。

在小型应用程序中，模型通常是概念上的分离，而不是物理上的分离。例如，如果应用程序只读取数据集并将其发送到视图，则应用程序没有物理模型层和关联的类。在这种情况下，数据集承担模型对象的角色。

### 视图

视图是显示应用程序用户界面(UI)的组件。通常，这个 UI 是从模型数据中创建的。例如，产品表的编辑视图根据产品对象的当前状态显示文本框、下拉列表和复选框。

### 控制器

控制器是处理用户交互、使用模型并最终选择显示 UI 的视图的组件。在 MVC 应用程序中，视图只显示信息；控制器处理并响应用户输入和交互。例如，控制器处理查询字符串值，并将这些值传递给模型，然后模型可能会使用这些值来查询数据库。

MVC 模式帮助您创建分离应用程序不同方面(输入逻辑、业务逻辑和 UI 逻辑)的应用程序，同时提供这些元素之间的松散耦合。模式指定了每种逻辑在应用程序中的位置。UI 逻辑属于视图。输入逻辑属于控制器。业务逻辑存在于模型中。这种分离有助于您在构建应用程序时管理复杂性，因为它使您能够一次专注于实现的一个方面。例如，您可以专注于视图，而不依赖于业务逻辑。

MVC 应用程序的三个主要组件之间的松散耦合也促进了并行开发。例如，一个开发人员可以处理视图，另一个开发人员可以处理控制器逻辑，第三个开发人员可以关注模型中的业务逻辑。模型-视图-控制器(MVC)设计模式是为了更好的软件可维护性而分离这些关注点的一个很好的例子。

## 存储库和数据映射器设计模式

MVC 模式帮助我们分离输入逻辑、业务逻辑和 UI 逻辑。但是，模型要负责的事情太多了。我们可以使用存储库模式将检索数据并将其映射到实体模型的逻辑与作用于模型的业务逻辑分离开来。业务逻辑应该与组成数据源层的数据类型无关。例如，数据源层可以是数据库、静态文件或 Web 服务。

存储库是应用程序的数据源层和业务层之间的中介。它向数据源查询数据，将数据从数据源映射到业务实体，并将业务实体中的更改持久化到数据源。存储库将业务逻辑与底层数据源的交互分离开来。数据层和业务层的分离有三个好处:

*   它集中了数据逻辑或 Web 服务访问逻辑。
*   它为单元测试提供了一个替代点。
*   它提供了一个灵活的体系结构，可以随着应用程序整体设计的发展而调整。

存储库代表客户端创建查询。存储库返回满足查询的一组匹配的实体。存储库还保存新的或更改的实体。下图显示了存储库与客户端和数据源的交互。

[![](img/8014200bd51db725c7c9e00ac84433d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y7V1zVb1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l2e1xo4vm9deyy403p78.png)

存储库是不同领域的数据和操作之间的桥梁。一个常见的情况是从一个数据是弱类型的域(如数据库)映射到一个对象是强类型的域(如域实体模型)。

存储库向数据源发出适当的查询，然后将结果集映射到外部公开的业务实体。存储库通常使用数据映射器模式在表示之间进行转换。

储存库消除了调用客户端对特定技术的依赖性。例如，如果客户机调用目录存储库来检索一些产品数据，它只需要使用目录存储库接口。例如，客户端不需要知道产品信息是通过对数据库的 SQL 查询还是对 SharePoint 列表的协作应用程序标记语言(CAML)查询来检索的。隔离这些类型的依赖关系为发展实现提供了灵活性。

## 洋葱架构

洋葱架构将应用程序划分为圆形层(像洋葱一样):

[![](img/7233534fbb4cac21e28b385a0901d011.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ps56SO9v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6y0l99fwf5vtfgqq3eva.png)

中心层是领域模型。当我们转向外层时，我们可以看到域服务、应用服务，最后是测试、基础设施和 UI 层。

在 DDD，一切的中心是所谓的“域”,域由两个主要部分组成:

*   领域模型
*   域服务

在函数式编程中，一个主要的架构原则是将副作用推到应用程序的边界。洋葱架构也遵循这个原则。应用程序核心(域服务和域模型)应该没有副作用和实现细节，这意味着不应该引用像数据持久性(例如 SQL)或数据传输(例如 HTTP)这样的实现细节。

域模型和域服务不知道任何关于数据库、协议、缓存或任何其他特定于实现的问题。应用程序核心只关心业务的特征和规则。外部层(基础设施、测试和用户界面)是与系统资源(网络、存储等)交互的层。)并且是副作用被隔离并远离应用程序核心的地方。

层与层之间的分离是通过接口的使用和依赖倒置原则的应用来实现的:组件应该依赖于抽象(接口)而不是具体化(类)。例如，基础设施层之一是 HTTP 层，它主要由控制器组成。名为`AircraftController`的控制器可以依赖名为
的接口

```
import { inject } from "inversify";
import { response, controller, httpGet } from "inversify-express-utils";
import * as express from "express";
import { AircraftRepository } from "@domain/interfaces";
import { Aircraft } from "@domain/entitites/aircraft";
import { TYPE } from "@domain/types";

@controller("/api/v1/aircraft")
export class AircraftController {

    @inject(TYPE.AircraftRepository) private readonly _aircraftRepository: AircraftRepository;

    @httpGet("/")
    public async get(@response() res: express.Response) {
        try {
            return await this._aircraftRepository.readAll();
        } catch (e) {
            res.status(500).send({ error: "Internal server error" });
        }

    }

    // ...

} 
```

Enter fullscreen mode Exit fullscreen mode

`AircraftController`是基础设施层的一部分，其主要职责是处理与 HTTP 相关的问题，并将工作委托给`AircraftRepository`。`AircraftRepository`实现应该完全不知道任何 HTTP 问题。此时，我们的依赖图如下所示:

[![](img/05679fc688f3b6b4055c11737e3cc118.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2z83OYMZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3mjknbo44vtstqvyk0vx.png)

图中的箭头有不同的含义,“comp”箭头定义了`AircraftRepository`是`AircraftController`(合成)的属性。“引用”箭头定义了`AircraftController`对`Aircraft`的引用或依赖。

`AircraftRepository`接口是域服务的一部分，而`AircraftController`和`AircraftRepository`实现是基础设施层的一部分:

[![](img/cc3f4a9c95319a46eb4e7271f867b506.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZPBHPETe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l5390ozl9u253sue5dps.png)

这意味着我们有一个从外层(基础设施)到内层(域服务)的引用。在洋葱架构中，我们只能从外层引用到内层，而不能从内层引用到外层:

[![](img/d3e73ed0e0db9c561a3f6bef25e745b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lY6d-UAl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2i73srvbean7nitmf8up.png)

我们使用`AircraftRepository`接口在设计时将领域层与基础设施层分离。然而，在运行时，这两层必须以某种方式连接起来。接口和实现之间的这种“连接”是由 InversifyJS 管理的。InversifyJS 允许使用`@inject` decorator 来声明要注入的依赖项。在设计时，我们可以声明我们希望注入一个接口的实现:

```
@inject(TYPE.AircraftRepository) private readonly _aircraftRepository: AircraftRepository; 
```

Enter fullscreen mode Exit fullscreen mode

在运行时，InversifyJS 将使用其配置注入一个实际的实现:

```
container.bind<AircraftRepository>(TYPE.AircraftRepository).to(AircraftRepositoryImpl); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在来看看属于域服务层的`AircratRepository`和`Repository<T>`接口。

```
import { Aircraft } from "@domain/entitites/aircraft";

export interface Repository<T> {
    readAll(): Promise<T[]>;
    readOneById(id: string): Promise<T>;
    // ...
}

export interface AircraftRepository extends Repository<Aircraft> {
    // Add custom methods here ...
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们的依赖图如下所示:

[![](img/501e662c24fc7584d3a7087cf6ad893c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1c1V4jxC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5bumjhjmjmeamndjabmy.png)

我们现在需要实现`Repository<T>`接口和`AircraftRepository`接口:

*   `Repository<T>`将由一个名为`GenericRepositoryImpl<D, E>`的类实现

*   `AircraftRepository`将由一个名为`AircraftRepositoryImpl`的类实现。

让我们从实现`Repository<T>` :
开始

```
import { injectable, unmanaged } from "inversify";
import { Repository } from "@domain/interfaces";
import { EntityDataMapper } from "@dal/interfaces";
import { Repository as TypeOrmRepository } from "typeorm";

@injectable()
export class GenericRepositoryImpl<TDomainEntity, TDalEntity> implements Repository<TDomainEntity> {

    private readonly _repository: TypeOrmRepository<TDalEntity>;
    private readonly _dataMapper: EntityDataMapper<TDomainEntity, TDalEntity>;

    public constructor(
        @unmanaged() repository: TypeOrmRepository<TDalEntity>,
        @unmanaged() dataMapper: EntityDataMapper<TDomainEntity, TDalEntity>
    ) {
        this._repository = repository;
        this._dataMapper = dataMapper;
    }

    public async readAll() {
        const entities = await this._repository.readAll();
        return entities.map((e) => this._dataMapper.toDomain(e));
    }

    public async readOneById(id: string) {
        const entity = await this._repository.readOne({ id });
        return this._dataMapper.toDomain(entity);
    }

    // ...

} 
```

Enter fullscreen mode Exit fullscreen mode

这个特定的`Repository<T>`实现期望通过其构造函数注入一个`EntityDataMapper`和一个`TypeOrmRepository`。然后，它使用这两个依赖项从数据库中读取数据，并将结果映射到域实体。

我们还需要`EntityDataMapper`接口:

```
export interface EntityDataMapper<Domain, Entity> {

    toDomain(entity: Entity): Domain;
    toDalEntity(domain: Domain): Entity;
} 
```

Enter fullscreen mode Exit fullscreen mode

而`EntityDataMapper`的实现:

```
import { toDateOrNull, toLocalDateOrNull } from "@lib/universal/utils/date_utils";
import { Aircraft } from "@domain/entitites/aircraft";
import { AircraftEntity } from "@dal/entities/aircraft";
import { EntityDataMapper } from "@dal/interfaces";

export class AircraftDataMapper implements EntityDataMapper<Aircraft, AircraftEntity> {

    public toDomain(entity: AircraftEntity): Aircraft {
        // ...
    }

    public toDalEntity(mortgage: Aircraft): AircraftEntity {
        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`EntityDataMapper`从由`TypeOrmRepository`返回的实体映射到我们的域实体。此时，我们的依赖图如下所示:

[![](img/9234ad974f9ffd9897de25f84f9132b7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pu-SrLly--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1kvlt30aqebjreb6x5ax.png)

我们终于可以实现`AircraftRepository` :

```
 import { inject, injectable } from "inversify";
import { Repository as TypeOrmRepository } from "typeorm";
import { AircraftRepository } from "@domain/interfaces";
import { Aircraft } from "@domain/entitites/aircraft";
import { GenericRepositoryImpl } from "@dal/generic_repository";
import { AircraftEntity } from "@dal/entities/aircraft";
import { AircraftDataMapper } from "@dal/data_mappers/aircraft";
import { TYPE } from "@dal/types";

@injectable()
export class AircraftRepositoryImpl
    extends GenericRepositoryImpl<Aircraft, AircraftEntity>
    implements AircraftRepository {

    public constructor(
        @inject(TYPE.TypeOrmRepositoryOfAircraftEntity) repository: TypeOrmRepository<AircraftEntity>
    ) {
        super(repository, new AircraftDataMapper())
    }

    // Add custom methods here ...

} 
```

Enter fullscreen mode Exit fullscreen mode

至此，我们完成了，我们的依赖图如下所示:

[![](img/18d4083d9f1d033d94a1c78c5d7c770e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vlB2elfo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4p6c8t9bbimoreb85kd2.png)

上图使用颜色来标识具体化(类，蓝色)和抽象(接口，橙色):

下图使用颜色来标识属于域层的组件(绿色)和属于基础结构层的组件(蓝色):

[![](img/01098c8bae070f92c815989aebf35307.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D_8nkttZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/152j7cxu3trb2e4cha5y.png)

在过去的十年里，这种架构在大型企业软件项目中对我非常有效。我最后还把一些巨大的整体洋葱分成了遵循相同架构的微服务。我喜欢说，当我们有实现洋葱架构的微服务时，我们就有了“一袋洋葱”。

我希望你喜欢这篇文章！请使用评论或通过 [@RemoHJansen](https://twitter.com/RemoHJansen) 让我知道你的想法。