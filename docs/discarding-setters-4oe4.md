# 丢弃设置器

> 原文：<https://dev.to/aleksikauppila/discarding-setters-4oe4>

处理类时，Setters 或 mutators 非常常用。几乎每个写过类的人都写过一些 setter 方法。我至少写过很多！它们如此常见，以至于大多数 ide 都提供了一种从类属性生成 getter 和 setter 方法的方法。就好像你被鼓励去写这些方法。

快讯:这是一个可怕的习惯。编写 setter 方法肯定会给代码库带来混乱和不稳定。Setter 方法是撒旦的后代！(看，我避开了通常的“是邪恶的”习语！)

那么，变异体有什么不好？我需要能够以某种方式改变类中的属性，对吗？

我的看法是，设置的东西都是关于配置的。我通过改变对象的属性来配置它的状态。对象是一个计算机程序，我试着像计算机一样思考。

这在软件开发领域是非常正常的做法。我们可以完全控制这类物体，因为我们可以随意改变它们的内部状态。但是我们真的能处理好在应用程序中跟踪状态的责任吗？这对任何一个开发者来说都是一个巨大的负担。

当我们有 setters 时，我们也非常需要 getters 来检查应用程序不同部分的状态。检查东西是很棒的...哦，等等，我的意思是这太糟糕了，每当我遇到一个对象，它的状态可以是属性的任意组合时，我都非常想提交我的辞呈。

```
 /**
 * @param JobContract[] $contracts
 * @return Money
 */
public function totalSalaries(array $contracts): Money
{
    $total = new Money();
    foreach ($contracts as $contract) {
        if ($contract->getStatus() !== "active") {
            // continue, throw exception, anything
        }
        if ($contract->getType() !== "monthly") {
            // continue, throw exception, anything
        }
        if ($contract->getEmployee() === null) {
            // continue, throw exception, anything
        }
        if ($contract->getSalary() === null) {
            // continue, throw exception, anything
        }
        $total->add($contact->getSalary()->getAmount());
    }
    return $total;
} 
```

Enter fullscreen mode Exit fullscreen mode

你在你工作过的代码库中见过这样的代码吗？有一大堆乱七八糟的代码，它们只关心是否有可能安全地使用一个对象`JobContract`。

在我们建模的问题空间中，事情真的经常发生吗？我表示怀疑。这是一个重要的思维转变。我们不应该通过属性来思考我们的模型。相反，我们应该思考他们的行为。当我们理解了对象应该如何表现时，我们就可以开始考虑它们需要知道什么来执行它们所宣传的行为。

考虑终止一个`JobContract` :

```
class JobContract
{
    //...
    private $endDate;
    private $status;
    //...
    public function setEndDate(DateTime $endDate): void
    {
        $this->endDate = clone $endDate;
    }

    public function setStatus(string $status): void
    {
        if (!in_array($status, self::STATUSES)) {
            throw InvalidArgumentException("Invalid status {$status}");
        }
        $this->status = $status;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

没有行为，只有属性。状态变化不受控制。终止合同的业务操作被卸载到其他地方。一些服务认为以这种方式配置对象代表了契约的终止。

```
class JobContract
{
    //...
    private $endDate;
    private $status;
    //...
    public function terminate(DateTime $terminationDate): void
    {
        $this->endDate = clone $terminationDate;
        $this->status = self::STATUS_TERMINATED;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

公共 API 更简单，反映了意图。状态变化被封装。没有人知道或关心这个类甚至有一个`$status`属性。业务操作清晰明了。

在很多情况下，对象的第一个实例化状态不是最终状态。总有一种方法可以自然地改变这种状态，而不需要使用 setter 方法。

**设定器喷射**。这是一个很酷的坏习惯的名字。Setter 注入基本上是一种在对象被实例化后对其进行配置的形式。

大概是这样:

```
class HttpTransfer implements LoggerAwareInterface
{
    private $httpClient;
    private $logger;

    public function __construct(ClientInterface $httpClient)
    {
        $this->httpClient = $httpClient;
    }

    public function setLogger(LoggerInterface $logger): void
    {
        $this->logger = $logger;
    }
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

setter 注入的目的是提供一种将*可选的*依赖项注入到对象中的方法。例如，有一个 PHP-FIG 批准的 [PSR](https://github.com/php-fig/log/blob/master/Psr/Log/LoggerAwareInterface.php) 使用 setter 注入(参见 LoggerAwareInterface)。或者再来一个:Symfony 的 [ContainerAwareInterface](https://github.com/symfony/symfony/blob/master/src/Symfony/Component/DependencyInjection/ContainerAwareInterface.php) 。开发人员将这些标准和框架视为良好实践的权威，因此很遗憾这些东西被遗漏了。

当你需要的时候，一切可能在那里，也可能不在那里，这增加了许多不必要的混乱。同样，您必须对该属性进行空检查，以确保您可以使用该服务。那些依赖关系真的是可选的吗？如果真的是这样的话，也许需要做一些设计上的考虑。

如何解决这个问题？通过构造函数注入所有内容。如果在某些环境中您不需要日志记录，那么可以配置为传递一个假的。这些“感知”接口还带有表示该服务的接口。如`ContainerInterface`或`LoggerInterface`。所以继续实现你的假货吧。真的就这么简单。

服务需要是无状态的。另一方面，实体和值对象需要一直处于有效状态。没有我们的破坏，编程已经够难的了。我知道关于 setters(和 getters)的主题已经写得很多了，但是这个问题似乎仍然存在。感谢您的阅读。