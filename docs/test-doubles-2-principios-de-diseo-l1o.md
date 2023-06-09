# 双试验(2)设计原则

> 原文：<https://dev.to/franiglesias/test-doubles-2-principios-de-diseo-l1o>

设计原则与测试密切相关，因此既是设计目标又是实现目标的工具。

在[上一篇](/test-doubles-1/)中，我们回顾了双试验及其类型。在此，我们将考虑应用设计原则和使用双试验。

对编写测试的影响可归纳为两个主要好处:

*   遵循设计原则的代码更易于测试。
*   如果折叠的班级遵循设计原则，创建*双重测试*就会简化。

另一方面，好的测试往往会导致我们设计得更好，正是因为更好的设计使我们更容易写出好的测试。这是一个良性循环。

换句话说，当测试程式码有困难时，程式码就有设计问题。

## 设计原则与双重

设计原理与双重测试之间的这种关系表现在许多方面。

正如我们所看到的，如果测试代码遵循设计原则，则编写测试会更容易，相反，如果我们看到构建测试很复杂，则表明我们应该更改测试代码的设计。

即使我们看到生成 double test 很复杂，这也会告诉我们，collaborative 类也存在问题，或者测试单元与 collaborative 之间的交互作用没有得到很好的介绍。

### 单一责任

只有一个原因需要更改的类将比具有多个责任的类更容易测试。

在开发双测试时，这一原则使我们受益，因为折叠类将更易于定义，因为它们只需要重现一种行为。

### 开放进行延伸，封闭进行修改

「开放/关闭」原则是指类别应该是开放的，因此您可以借由延伸类别而不变更其程式码来修改其行为。

当我们创建双重测试时，我们想要得到的是一个相当于真实的对象，但具有不同的行为。因此，在许多情况下，我们将继承原始类以创建 double 测试，或者最好是实现其接口。

然后应用该原则，即不应修改对象或类来测试它，也不应将其用作测试中的重复对象。

### liskov 的替换

Liskov 的替换原则是我们进行双重测试可能性的基础。

此原则指出，在类别阶层中，基底类别和衍生类别必须是可交换的，而不需变更使用它们的程式码。

创建双测试时，我们要么扩展类，要么实施接口。遵守这一原则使我们能够输入重复项，而无需触摸测试中的类代码。

因此，双测试在与我们测试的驱动器相关的接口方面必须遵守这一原则。

### 界面分离

接口隔离原则告诉我们，类不应依赖于它不需要的功能。换句话说，接口应只显示所需的方法。如有必要，一个类将实现多个接口。

这一原则应用得越好，就越容易产生重复，测试就越不复杂，也就越精确，因为我们不需要模拟大量的行为。

如果一个协作类有二十种方法，那么在创建其 double 时，我们需要实施这二十种方法，尽管我们只对两种方法感兴趣。在这种情况下，最好将这两种方法提取到一个接口中，然后从该接口创建副本。这将使我们能够更好地分担责任，从而获得额外的益处。

### 相依性投资

依赖投资告诉我们，我们必须永远依赖抽象。这样，改变我们使用的部署就像注入不同的东西一样微不足道。

如果我们依赖抽象，而且没有比接口更抽象的东西，则很容易为测试情形生成具体的实施。

### 痴呆法或最低知识法

Demeter 定律告诉我们，物体不应该知道其他物体在内部是如何工作的。

如果一个对象 a 使用另一个对象 b，而另一个对象 b 又在内部使用第三个对象 c 来响应此请求，则 a 不应知道 c 的任何内容。

对于测试来说，这意味着对于许多可能具有折叠类的依赖项，我们不需要它们来创建副本，因为我们只关心它们的接口或行为，而不关心它们的结构。如果我们需要它们，那就是我们的设计有问题。

### 干:避免重复

守则中的任何重复都应导致我们找到一个反应堆，以便减少或消除该反应堆。

这项原则不仅适用于测试的程式码，而且也适用于测试本身，当然也适用于双测试。

干法原则不一定是事先设计的，但我们可以在检测到代码重复时加以应用。例如，如果我们看到第三次使用相同的 test-double，则可能需要将其移动到 Case 测试属性并在安装程序中初始化该属性。

### 八木:你不会需要它的

Yagni 原则提醒我们不要发展我们现在不需要的东西。

因此，我们的双重测试必须满足我们创建它时的具体需要。双重测试可以从一个测试中的简单虚拟开始，变成另一个测试中的 Mock。

## 一个例子

下面是一个小型测试代码示例，您可以在其中看到几个正在运行的设计原则。

假设一项服务`SendNotificationService`使用`Mailer`发送消息。

Veamos un par de posibles 测试:

```
use Dojo\MailerExample\Application\SendNotificationService;

use PHPUnit\Framework\TestCase;

interface Mailer
{
    public function send(Message $message): bool;
}

class SuccessfulMailerStub implements Mailer
{
    public function send(Message $message): bool
    {
        return true;
    }
}

class FailedMailerStub implements Mailer
{
    public function send(Message $message): bool
    {
        throw new MailServiceDownException();
    }
}

interface Message
{
    public function subject(): string;
    public function body(): string;
}

class MessageStub implements Message
{
    public function subject(): string
    {
        return 'Subject';
    }
    public function body(): string
    {
        return 'Body';
    }
}

class SendNotificationServiceTest extends TestCase
{
    public function testSendNotificationCanSendAMessage() : void
    {
        $message = new MessageStub();
        $mailer = new SuccessfulMailerStub();

        $sendNotification = new SendNotificationService($mailer);
        $this->assertTrue($sendNotification->send($message));
    }

    public function testSendNotificationCanNotSendMessage() : void
    {
        $this->expectException(NotificationCouldNotBeSent::class);

        $message = new MessageStub();
        $mailer = new FailedMailerStub();

        $sendNotification = new SendNotificationService($mailer);
        $sendNotification->send($message);
    }
} 
```

`Mailer`和`Message`都是接口，因此服务不依赖于任何具体的实施。在项目中，我们可以使用 SwiftMailer 作为示例，但没有什么可以阻止我们使用 Twitter、Slack、telegram…，只要编写一个符合“`Mailer`接口”的适配器即可。

在测试中，具体实施与我们无关。我们只希望我们的服务尝试发送消息，如果无法发送，则返回异常。

应用依存性投资，即依赖界面时，我们可以准备*存根*再现我们需要的行为，而不会过分夸张。在本例中，消息已成功发送(邮件程序将返回 true)。

另一方面，`Mailer`显然只有一项责任，就是发信息，所以其行为很容易模拟。还应看到开放/封闭和 Liskov 原则得到遵守。我们对`Mailer`界面只有一种方法感兴趣，这一事实告诉我们，我们也应用界面隔离:我们的具体实现可能有其他方法，但对于这种情况，我们只想要一种方法。

`Message`在这里充当虚拟的，我们不想让你做任何特别的事情，但我们需要它来完成界面。

我留下变量是为了使测试更容易阅读，但可以通过做一个‘t0’内嵌变量来消除，因为我们不用对它们做任何事情。测试结果如下:

```
class SendNotificationServiceTest extends TestCase
{
    public function testSendNotificationCanSendAMessage() : void
    {
        $sendNotification = new SendNotificationService(new SuccessfulMailerStub());
        $this->assertTrue($sendNotification->send(new MessageStub()));
    }

    public function testSendNotificationCanNotSendMessage() : void
    {
        $this->expectException(NotficationCouldNotBeSent::class);
        $sendNotification = new SendNotificationService(new FailedMailerStub());
        $sendNotification->send(new MessageStub());
    }
} 
```

我们刚才做的这个小反应釜有助于在示例的有限范围内举例说明 DRY。另一种选择是将`$message`移出测试用例的某个属性，以便将其重新组合:

```
class SendNotificationServiceTest extends TestCase
{
    private $message;

    public function setUp() : void
    {
        $this->message = new MessageStub();
    }

    public function testSendNotificationCanSendAMessage() : void
    {
        $mailer = new SuccessfulMailerStub();

        $sendNotification = new SendNotificationService($mailer);
        $this->assertTrue($sendNotification->send($this->message));
    }

    public function testSendNotificationCanNotSendMessage() : void
    {
        $this->expectException(NotiicationCouldNotBeSent::class);
        $mailer = new FailedMailerStub();

        $sendNotification = new SendNotificationService($mailer);
        $sendNotification->send($this->message);
    }
} 
```

简而言之，一些原则帮助我们实现另一些原则。

特别是，通过反向依赖关系和仅依赖一个简单的界面，我们正在测试的课程无法绑定到特定的实施，这有助于遵守 Demeter 的法律，因为在开发时，我们可能还没有决定如何分发此类通知。

YAGNI 原则也是如此。我们的服务不需要为将来可能使用的部署做好准备，只需要知道如何使用‘t0’。虽然我们最初打算使用电子邮件，但我们可以在一段时间内通过 Slack 发布。

## 总之

设计原则不仅适用于生产代码，而且应贯穿整个开发过程，包括在需要时进行双重测试和测试。