# 测试双打(1)

> 原文：<https://dev.to/franiglesias/test-doubles-1-fei>

我正考虑以*双重测验*的比喻手法开始文章，作为电影专家，他们在某些场景中对演员进行配音，不一定是危险的。但当我越转身，我越不清楚，我必须是一个好兆头。

毕竟，*双重测试*更是形象化，有时在现场有一两行对话，而我们测试的单位是主角，必须承担演出的重量:我们不能用另一行代替。相比之下，在*双重测验*中，我们倾向于不做任何特殊的事情，如果做了，就不要离开剧本，也不要一毫米。

所以，∞是‘t0’双重测试〖t1〗吗？

## 双重测试的概念

让我们从**单元测试**的想法开始。单元测试试图证明软件单元按预期方式工作。

是软件单元吗？这通常是近东救济工程处的一项职能或一个类别，通过其公共方法来界定其可观察到的行为。

但是，许多类将使用其他类作为合作伙伴，这就带来了问题:一类的可观察行为中有一部分符合其自身的代码，而哪一部分符合其合作伙伴的代码？

为了区别对待这种情况，我们必须控制这些合作者的行为。

这与我们进行科学实验非常相似:如果我们要说某些变化是由于我们正在研究的一个因素造成的，我们就必须控制可能影响到的其他变量。

在某些情况下，我们可以删除它们。例如，在真空室里做一个实验，以避免空气中的锈蚀效应。

在其他情况下，我们无法做到这一点，我们必须使用其他技术，例如随机技术，这将使我们在我们所看到的变化范围内有一个可预见的误差范围，或控制这些变化:确切知道我们在什么条件下对这些变量进行试验，并在不同条件下重复试验。

那么，在‘t0’双重测试’的话题上，策略是这样的。目的是让它们对我们测试的单位的行为产生零影响，或者我们可以控制它。

## 双重测试的原因和目的

我们使用*双重测试*来避免测试中意外的影响，并确保我们测试的行为符合软件单元:

*   控制合作伙伴或依赖项的行为，并生成各种方案来测试我们的软件单元。
*   测试不受某些资源可用性的影响。例如，我们可以用返回给我们特定答案的双重服务来代替外部服务，甚至可以假装它不可用，以确保我们的代码能够对这种情况作出反应。
*   通过模拟可能会降低性能、消耗大量资源(如数据库等)的系统组件，可以更快地运行测试。
*   避免使用实际数据或生产数据。

## 双重测试，在哪里可以找到

有几种*双重测验*虽然我们倾向于叫它们全部*mock*。但严格来说，mocks 是一种特殊类型的双重测试。

[小嘲笑者](https://8thlight.com/blog/uncle-bob/2014/05/14/TheLittleMocker.html)

我们将根据您是否准备好实施测试对它们进行分组。这就是:有*双重测试*希望与一定的模式配合使用，这反映在测试中。如果该使用模式发生变化，测试将失败。这就是为什么我们说它们会导致测试与 SUT(T2【测试主体】的实现发生耦合，从而使测试变得脆弱。我们稍后再谈这个。

### 不配合试验实施的双重试验

傻瓜和存根对它们的使用没有期望，只限于参与技执委的行为。

#### 哑弹

**傻瓜**是我们创造的双重性，因为我们对他们的界面感兴趣，而不是他们的行为感兴趣。很明显，我们的 SUT 给他们打电话，但他不期望得到任何反应，或者他的行为不依赖于她。因此，*虚拟*不应实施行为。简而言之，一个*虚拟*:

*   实现接口

典型的情况是，当您需要来自施工人员的注射时，可以实例化测试对象。

```
namespace Tests\Dojo\Doubles;

use Dojo\Doubles\SomeService;
use PHPUnit\Framework\TestCase;

class DummyLogger implements LoggerInterface
{
    //....
}

class SomeServiceTest extends TestCase
{
    public function testSomeServiceCanBeInstantiated()
    {
        $logger = new DummyLooger();
        $someService = new SomeService($logger);
        $this->assertInstanceOf(SomeService::class, $someService);
    }
} 
```

在上例中，我们的 SUT ( `SomeService`使用了`Logger`，但我们不会看它记录了什么。

#### 存根

在大多数情况下，双重测试是不够的:我们通常希望合作伙伴对我们的 SUT 提供答案。例如，我们可能需要一个服务来查询当前的日期和时间，或者另一个服务来告诉我们某个用户是否有效，或者您可能会想到的任何示例。

为此，我们需要另一种称为存根的双测试。存根是具有以下特征的对象:

*   实现接口
*   它具有预定的行为:调用其中一种方法会返回已知的响应

下面是一个例子*存根*。`ClockServiceStub`类将始终为我们提供与实例化该类中的对象时所安排的时间相同的时间。这样，我们就永远知道什么日期或时间会还给我们，这种情况不会发生在真正的课堂上。

```
use DateTimeImmutable;

interface ClockServiceInterface
{
    public function getCurrentDateTime() : DateTimeImmutable;
}

class ClockServiceStub implements ClockServiceInterface
{
    /**
     * @var DateTimeImmutable
     */
    private $date;

    public function __construct(string $dateString)
    {
        $this->date = new DateTimeImmutable($dateString);
    }

    public function getCurrentDateTime() : DateTimeImmutable
    {
        return $this->date;
    }
}

$dateForTesting = new ClockServiceStub('2018-03-12'); 
```

### 双耦合试验

**mock**和 **Spies** 对它们如何被 SUT 使用保持着期待，也就是说，它们就它们是否被具体地调用作出断言。

问题是，如果 SUT 的实施发生变化，即使行为保持不变，测试结果也可能发生变化，因为对使用伙伴的期望没有达到。

举个简单的例子。假设测试方法对发送电子邮件的服务进行两次调用，因为我们要通知两个收件人某个特定情况，所以我们对将被调用两次的电子邮件服务进行重复。因此，测试等待两次调用，并且只要实施同时执行这两次调用就会通过。

但是现在，想象一下我们的电子邮件服务可以用一个电话发送一个地址列表。如果我们将实施更改为这样，我们的测试将失败，因为它等待两次呼叫，并且只执行一次呼叫。但是，SUT 的行为仍然正确，因为发送了两封电子邮件。

那么，如果行为正确，测试失败的原因是什么？因为这些*双重测试*可以通过我们为他们安排的期望，产生从测试到 SUT 实施的耦合。

#### 间谍

Un **Spy** 是*存根*，除此之外，它还保存了关于其命名方式的信息，因此我们可以就此信息作出断言。这意味着测试与技术咨询方案的实施相吻合，引入了一个需要考虑的脆弱性因素。

总之，一个*间谍*:

*   实现接口
*   他有预定的行为
*   它让我们在测试中验证它是否以某种方式被使用
*   在测试中引入脆弱性

以下是一个非常典型的例子，说明假想的“T2”间谍是什么样的。`Mailer`。*间谍*只限于计算调用`send`方法的次数，这使我们能够在测试中进行断言。

```
interface Mailer
{
    public function send(Message $message) : void;
}

class MailerSpy implements Mailer
{
    private $calls = 0;

    public function send(Message $message) : void
    {
        $this->calls++;
    }

    public function getCalls()
    {
        return $this->calls;
    }
}

class ServiceTest extends TestCase
{
    public function testMailer()
    {
        $mailerSpy = new MailerSpy();
        $sut = new Service($mailerSpy);
        $sut->execute();
        $this->assertEquals(2, $mailerSpy->getCalls());
    }
} 
```

#### 嘲弄

**Mock** 是一种 *Spy* 希望被 SUT 以特定方式使用，例如用某些参数调用方法。如果这种期望没有实现，测试就不会成功。

就像一个存根一样，它有预定的反应，甚至有几个。不同之处在于，让他们期待某种特定的用途会产生一个隐含的断言，驻留在 *Mock* 上，而不是在测试中。

简而言之，一个 mock:

*   实现接口
*   他有预定的行为
*   希望能以某种方式使用
*   在测试中引入脆弱性

mocks 需要比 Spies 更复杂的编程，所以我们将在接下来的文章中看到如何生成它们。现在，我们有一个使用预言的例子

```
class ServiceTest extends TestCase
{
    public function testMailer()
    {
        $mailerProphecy = $this->prophesize(Mailer::class);
        $mailerProphecy->send(Argument::type(Message::class))
            ->shouldBeCalled();
        $sut = new Service($mailerProphecy->reveal());
        $sut->execute();
    }
} 
```

### *双重测试*是替代实现

#### 假的

Fake 是专为在测试情况下使用而创建的类接口的实现。因此，它有业务行为，实际上需要自己的测试来确保这是正确的。

制造假的原因有很多。最重要的可能是在生产部署不受限制的情况下执行集成测试，例如访问数据库和其他可能会失败的远程资源(如内存中部署的存储库)。

## 产生双重测试的备选方案

### 使用真实班级

在许多情况下，使用双重测试是没有意义的。相反，我们将在测试中使用实际类:

*   **价值对象**:VO，顾名思义，不可能有*侧效应*或依赖性，所以在测试中使用它们时，我们可以确信其对织物的影响是预期的。
*   **DTO** :它们不会停止没有行为的物体，所以我们可以毫无问题地使用它们。
*   **请求**、**命令**、**事件**:不包含逻辑的对象不需要折叠。
*   任何没有*侧效应*或从属关系的其他类。

### 直接实施

基本上，它是通过实现所需的接口来创建对象，并且其行为为 nulo 或仅限于我们在任何类型的测试中使用该接口时所需的行为。

如果这种实现包括业务逻辑，我们将谈论的是假的。

### 自分流

*自我分流*是一种相当有趣的技术，通过实现我们需要再现的界面，使我们能够像间谍一样收集信息，从而使测试本身成为双重测试。

这显然不是一种通常使用的技术，但在开发的早期阶段，当我们尚未创建协作者并希望了解其界面时，或者当界面非常简单且只有一种或两种方法时，这种技术可能非常实用。

这是邮件程序的自转发版本。

```
class ServiceTest extends TestCase implements Mailer
{
    private $mailerCalls = 0;

    public function testMailer()
    {
        $sut = new Service($this);
        $sut->execute();
        $this->assertEquals(2, $this->getCalls());
    }

    public function send(Message $message) : void
    {
        $this->mailerCalls++;
    }
} 
```

从长远来看，我们会在开发过程中消除自我导向器，因此，我们会重新设计测试。

Michael Feathers [介绍了本文](https://www.yumpu.com/en/document/view/47929352/the-self-shunt-unit-testing-pattern-object-mentor)中的自分流。也有意思的是看一看[这篇比较了几种莫克思](https://8thlight.com/blog/paul-pagel/2006/09/11/self-shunt.html)创作方法的文章

### 匿名班

从 PHP 7.1 开始，我们可以使用匿名类。当我们需要不在测试之外重复使用的简单物件时，这在测试中非常有用。

下面是 Mailer 使用此技术的前一个示例:

```
class ServiceTest extends TestCase
{
    public function testMailer()
    {
        $mailer = new class implements Mailer {

            private $calls = 0;

            public function send(Message $message) : void
            {
                $this->calls++;
            }

            public function getCalls()
            {
                return $this->calls;
            }
        };

        $sut = new Service($mailer);
        $sut->execute();
        $this->assertEquals(2, $mailer->getCalls());
    }
} 
```

## 终止

本文对双重测试类型进行了综述。在接下来的交货中，我们将看到如何使用它们进行测试。