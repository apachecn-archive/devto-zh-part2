# 依赖性和耦合性

> 原文：<https://dev.to/franiglesias/dependencies-and-coupling-4365>

如何应用依赖倒置原则，理解什么是依赖和耦合。以及在创建对象时如何知道何时需要使用注入或实例化。

在编程中，我们常说当一个软件模块利用另一个模块来完成它的工作时，就建立了一个**依赖关系**。如果我们谈论类，我们说当`Client`使用`Service`来完成他们自己的职责时，一个类(`Client`)依赖于另一个类(`Service`)。显示依赖关系是因为没有`Service`类`Client`类就不能工作。

软件依赖本身并不坏(从他们在一些帖子中谈论软件依赖的方式来看似乎如此)。依赖的问题是一个层次的问题。要知道，有强依赖和轻依赖。这里的关键是我们如何管理它们，使它们尽可能轻。

事实上，依赖关系的存在是一个很好的暗示，因为它可能指向这样一个事实，即我们遵守单一责任原则，让一些类将超出它们范围的任务委托给其他类。

软件单元之间的依赖程度被称为**耦合**。如果我们想用另一个类替换`Service`类，那么当我们必须重写`Client`类时，我们说有一个*大腿耦合*。这违反了**打开/关闭原则**。相反，当我们可以用另一个类替换`Service`类而不必接触`Client`类时，我们说存在*松(低)耦合*。

我们如何做到这一点？通过使用三种工具:

*   依赖注入模式
*   立体中的三维:依存倒置原理
*   适配器模式

## 隐藏依赖关系

让我们从最坏的情况开始:类`Client`使用类`Service`而没有人能猜到。让我们看一个例子:

```
class Client {
    private $service;

    public function __construct() {
        $this->service = new Service();
    }
    public function doSomething() {
        $this->service->doTask();
    }
}

class Service {
    public function doTask() {
        echo 'Performed by Service';
    }
}

$Client = new Client();
$Client->doSomething(); 
```

要知道`Client`使用`Service`我们应该研究一下源代码，因为我们从它的公共接口看不到任何东西。

这里是开/闭原则违背发生的地方。以这种方式构造，类`Client`可以修改，为了改变它的行为，我们必须重写它。

在这种情况下，耦合是最大的，当我们因任何原因必须触摸`Service`时，`Client`功能可能会中断。例如，假设`Service`是一个来自第三方包或库的类，作者决定执行一个主要的升级并改变`Client`使用的方法的接口。即使可以完全访问 PHP 代码，你也可以想象维护会是怎样的一场噩梦，以及其中包含的风险。事实上，你必须呆在`Service`的固定版本中，忘记升级。

要处理这个问题，有一个简单的解决方案，就是应用**依赖注入模式**:

```
class Client {
    private $service;

    public function __construct(Service $service) {
        $this->service = $service;
    }
    public function doSomething() {
        $this->service->doTask();
    }
}

class Service {
    public function doTask() {
        echo 'Performed by Service';
    }
}

$Client = new Client(new Service());
$Client->doSomething(); 
```

简单地说，依赖注入模式包括通过构造函数(或设置器)注入依赖。现在，依赖变成**可见**。还有大腿耦合，但是现在我们有了更多的自由，因为我们知道类是如何关联的。

## 依存倒置

依赖倒置是将类或模块之间的耦合性降低到最小的方法。依赖性反转原则声明:

*   高级模块不应该依赖于低级模块。两者都应该依赖于抽象。
*   抽象不应该依赖于细节，细节应该依赖于抽象。

总而言之:每一个依赖都应该发生在抽象上，而不是具体的实现上。

在我们的例子中，依赖现在是显式的，这是好的，但是`Client`一直依赖于`Service`的具体实现，这是不好的。

要反转依赖性，我们应该执行以下操作:

`Client`不应该等待一个`Service`的具体实例，相反，它应该等待一个满足一定条件的类，这就相当于:它应该履行一个契约。正如我们所知，编程中的契约是一个 T2 接口 T3。接口是我们在软件中能得到的最抽象的东西。

另一方面，`Service`应该尊重被允许由`Client`使用的接口，就是这样:它应该依赖于那个抽象。

因此，我们需要创建一个接口，让`Client`等待任何实现它的类。如何定义接口？你应该从`Client`的需求或兴趣出发，`Service`必须接受这一点。

```
interface ServiceInterface {
    public function doTheThing();
}

class Client {
    private $service;

    public function __construct(ServiceInterface $service) {
        $this->service = $service;
    }
    public function doSomething() {
        $this->service->doTheThing();
    }
}

class Service {
    public function doTask() {
        echo 'Performed by Service';
    }
}

$Client = new Client(new Service());
$Client->doSomething(); 
```

显示的代码还不能工作:`Service`没有实现`ServiceInterface`，所以`Client`不会接受它。

为什么我改变了`Client`使用`Service`的方式？为什么我改变了`Client`调用的方法？简单地说，因为我想说明应该如何从`Client`的需求出发来设计接口，并展示我们如何在不改变代码的情况下让`Service`满足该接口。

在代码中，`Client`依赖于`ServiceInterface`，这意味着它等待一个实现了方法`doTheThing()`的类。不过，`Service`没有这个方法。为了解决这个问题，我们必须修改类`Service`，实现`ServiceInterface`，或者应用**适配器模式**来使用类`Service`来支持`ServiceInterface`。

适配器是使用另一个类实现接口的类，因此我们将在代码中包含适配器:

```
interface ServiceInterface {
    public function doTheThing();
}

class Client {
    private $service;

    public function __construct(ServiceInterface $service) {
        $this->service = $service;
    }
    public function doSomething() {
        $this->service->doTheThing();
    }
}

// We don't touch Service

class Service {
    public function doTask() {
        echo 'Performed by Service';
    }
}

// We create an adapter

class ServiceAdapter implements ServiceInterface {
    private $service;

    public function __construct(Service $service)
    {
        $this->service = $service;
    }

    public function doTheThing()
    {
        $this->service->doTask();
    }
}

$Client = new Client(new ServiceAdapter(new Service()));
$Client->doSomething(); 
```

调用变得有点复杂，但好处是巨大的，因为我们已经将耦合降低到更低。

从现在开始，类`Client`和`Service`可以在接口不变的情况下彼此独立地变化和发展(除了非常重要的动机之外，接口在时间上应该是稳定的)。如果有必要，我们将不得不在`Service`改变接口的情况下修改`ServiceAdapter`。

在下面的例子中，我们假设`Service`经历了一个破坏向后兼容性的变化:

```
interface ServiceInterface {
    public function doTheThing();
}

class Client {
    private $service;

    public function __construct(ServiceInterface $service) {
        $this->service = $service;
    }
    public function doSomething() {
        $this->service->doTheThing();
    }
}

// Service has change its public interface

class Service {
    public function doService() {
        echo 'Performed by Service';
    }
}

// We change Adapter according to the changes in Service

class ServiceAdapter implements ServiceInterface {
    private $service;

    public function __construct(Service $service)
    {
        $this->service = $service;
    }

    // We need to change the way the adapter uses Service

    public function doTheThing()
    {
        $this->service->doService();
    }
}

$Client = new Client(new ServiceAdapter(new Service()));
$Client->doSomething(); 
```

更多:我们可以用任何其他类来代替`Service`，这些类通过自身或适配器来实现接口`ServiceInterface`。通过这种方式，我们可以修改`Client`的行为，而不触及它的代码，遵守开放/关闭原则。

现在，我们添加一个新的类来代替`Service`:

```
interface ServiceInterface {
    public function doTheThing();
}

class Client {
    private $service;

    public function __construct(ServiceInterface $service) {
        $this->service = $service;
    }
    public function doSomething() {
        $this->service->doTheThing();
    }
}

class Service {
    public function doService() {
        echo 'Performed by Service';
    }
}

class ServiceAdapter implements ServiceInterface {
    private $service;

    public function __construct(Service $service)
    {
        $this->service = $service;
    }

    public function doTheThing()
    {
        $this->service->doService();
    }
}

class NewService {
    public function theMethod()
    {
        echo 'Performed by New Service';
    }
}

class NewServiceAdapter implements ServiceInterface {
    private $service;

    public function __construct(NewService $service)
    {
        $this->service = $service;
    }

    public function doTheThing()
    {
        $this->service->theMethod();
    }

}

$Client = new Client(new ServiceAdapter(new Service()));
$Client->doSomething();
echo chr(10);

$Client2 = new Client(new NewServiceAdapter(new NewService()));
$Client2->doSomething(); 
```

### 关于适配器

适配器和适配类之间有一个大腿耦合。显然，我们不能把它们分开。这种耦合不是一个问题，因为从我们的`Client`的角度来看，适配器“就是”`Service`，并且它不担心它是如何实现的，因为接口是受尊重的。另外，适配器是一个代码简单的类，它限制自己翻译`Client`和`Service`之间的消息。

这可能会让我们考虑隐藏依赖关系，并使其隐式化，作为在实例化适配器时节省一点代码的一种方式。但这不是个好主意。所有的依赖关系都应该是显式的。

## 当依赖注入变得复杂时

正如您在前面的例子中看到的，在实例化`Client`类时，解耦增加了一点复杂性。如果它有几个依赖项，每个依赖项都可以使用自己的适配器，那么依赖项注入会变得乏味，但并不复杂。尽管如此，这意味着您需要在应用程序的不同部分重复大量代码来实例化某个对象。

解决这个问题的方法是使用一些创造性的模式，比如工厂、构建者、池或原型，来自动实现实例化。

另一个解决方案是所谓的依赖注入容器(DIC ),也就是说，一个负责为我们提供完全组装的对象的类。DIC 利用了几种创建模式，因此我们可以注册对象应该被实例化的方式。

## 不一定非要解耦

耦合应该在`Client`类行为的上下文中考虑。这使用了`Service`类的一个行为，所以它可以完成自己的行为。

这并不是在所有情况下都一样。以值对象为例。这些并不以同样的方式促成行为。值对象的使用方式与基本类型类似，它们的行为旨在为保护自身不变量的类提供服务。值对象是不可变的，没有外部依赖性，或者，如果有，它们在另一个值对象上。因此，这些对象可以用一个简单的`new`或者一个命名的构造函数(如果提供的话)来实例化。

因此，我们可以区分“可更新对象”和“可注入对象”。[miko he very 解释得很好](http://misko.hevery.com/2008/09/30/to-new-or-not-to-new/)。总而言之:

可更新对象是那些在创建时需要一些可变参数的对象。事实上，我们每次都需要一个新的对象。设想一个 Email 类，它表示一个电子邮件地址甚至一条消息(它不发送它，只表示它，但也有能力验证它并确保它是精心制作的)。我们将需要为每个电子邮件地址或消息不同的对象。参见示例:

```
class Email {
    private $address;

    function __construct($address) {
        if (filter_var($address, FILTER_VALIDATE_EMAIL) === false) {
            throw new InvalidArgumentException("$address is not a valid email");
        }
        $this->address = $address;
    }

    public function getAdress()
    {
        return $this->address;
    }

}

$myEmail = new Email('franiglesias@mac.com');
$otherEmail = new Email('other@example.com'); 
```

不能注入新的对象，因为依赖注入容器不知道每个具体实例需要什么参数。我们可以有一个工厂来传递参数，它将返回对象，但在这种情况下，`new`是一个和其他工厂一样好的工厂。事实上，在许多情况下，它可能是最合适的。

新东西不需要显式接口，因为你不需要各种选项。一个可更新的对象可以通过继承的方式扩展另一个，在这种情况下，如果我们遵守 **Liskov 替换原则**，基类将成为“接口”,所以两个类将是可互换的。

另一方面，可注入对象是可以在 DIC 的帮助下构造的对象，因为它们不需要在每次实例化时都改变的参数。它们过去有接口，因为它们应该是可替换的，也就是说:我们希望使用的类与它们有松散的耦合。

注射剂和新制剂不得混合。一个可注入的不能用一个可更新的来创建，一个可更新的也不能用一个可注入的来构造(如果这样的话，它就变成了可注入的)。这并不是说一个不能使用另一个，而是它们应该作为参数在方法中传递。