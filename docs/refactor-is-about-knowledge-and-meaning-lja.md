# 重构是关于知识和意义的

> 原文：<https://dev.to/franiglesias/refactor-is-about-knowledge-and-meaning-lja>

重构不仅仅是一项技术任务。重构具有商业价值，因为它的主要目的是确保业务语言和知识反映到代码中。

## 遗留的是未经测试的代码

这句来自 Michael Feathers 的话可能有点夸张，但它很好地定义了对抗遗留代码的含义。测试的不存在反映了我们对代码如何工作的无知，这种无知是我们遗留问题的基础。

有时，我们可能会说或听别人说，我们不清楚产品中的一段代码是如何工作的。首先，我们甚至想知道它是如何工作的。虽然它工作正常，但奇怪和难以调试的错误是常见的。测试，如果有的话，应该为我们提供代码不能交流的信息，或者至少，它们应该允许我们继续进行重构，帮助更好地组织事情。

但是，考虑到我们并不总是可以依靠它们:有没有可能在没有测试的情况下重构？

## 无测试重构

事实上，不通过测试进行重构是可能的，至少在一定的范围内，并且是用测试覆盖代码的第一步。

主要是，我们在 IDE 中包含了自动重构工具，一般来说，这些工具为我们提供了一定数量的安全重构，因为它们不会改变代码的行为。即使没有测试，我们也可以相信这些变化。

当考虑重构的范围时，这种信任得到了加强。例如，如果您将一个类中的私有方法的名称更改为更具描述性，您可以确保这种更改不会影响代码的其他部分。如果您提取方法来模块化其他更大的方法，也会发生同样的情况。

但是，为什么要重构有效的代码呢？它提供了什么价值？

我们重构主要是为了反映我们对代码的了解。

当我们研究一段遗留代码时，通常目标是修复问题或添加功能，我们正在发展我们对它如何工作的知识。有时，我们可以简单地通过阅读代码来解释代码做了什么。其他时候，我们建立关于我们认为它如何工作的假设，并试图用它产生的结果来证实这一点。

此时，我们可以重构来实现两个主要目标:

*   反映我们对代码如何工作的了解，以一种更容易理解的方式重写它。
*   将系统置于可测试状态。

## 重构作为关于代码知识的反映

当我们阅读代码时，我们会了解它是如何工作的，以及我们在代码中发现的每个符号代表什么。问题是:这些知识储存在哪里？只是在我们的脑海里？

重构是我们将代码知识放入代码本身的方式。

让我们看一个简单的例子。假设下面的片段:

```
$this->userRepository->save($user);
$message = new Message();
$message->setTo($user->email());
$this->mailer->send($message); 
```

考虑到上下文，并做了一些尝试和错误，我们肯定会得出结论:`$message`指的是确认消息，报告用户刚刚在系统中被创建。

因此，我们可以轻松地更改变量的名称，以反映我们刚刚学到的内容:

```
$this->userRepository->save($user);
$confirmationMessage = new Message();
$confirmationMessage->setTo($user->email());
$this->mailer->send($confirmationMessage); 
```

或者更具体地说:

```
$this->userRepository->save($user);
$userWasCreatedMessage = new Message();
$userWasCreatedMessage->setTo($user->email());
$this->mailer->send($userWasCreatedMessage); 
```

这种重构(重命名)不会改变功能，但它使代码更精确地描述了正在发生的事情。如果在原始代码中需要努力解释代码在做什么，那么在重构版本中这种努力是不必要的，因为代码对此非常清楚。

此外，在代码本身中收集了这些知识之后，将来对其进行干预将会更加容易，这将节省时间和精力。

## 应用重构

### 重命名

在编程世界中，命名事物给人留下了很坏的印象。

更改名称允许我们在代码本身中反映我们对系统的了解，消除歧义、过于通用的名称、不适当的或模棱两可的名称。

在上一节中，我们展示了一个通过重命名进行重构的例子。这是另一个例子，我们用另一个详细描述了我们对文件所做的事情的名字来改变一个过于普通的名字。

```
public function processFile($file);

// vs

public function extractProductInformationFromFile($file); 
```

几十年前，当内存和存储成本太高时，我们不得不节约使用只有一个字符长度的神秘变量名。因为很长时间以来，我们可以而且必须允许我们为任何编程元素使用表达性名称。

让我们想象一个 for 循环:

```
for ($i = 0; $i <= $max, $i++) {
    fwrite($file, $lines[$i]);
} 
```

这段代码说明了什么？显然它是在讨论一个数组，一行一行地写在一个文件中，直到达到一个极限。为什么不用它所有的字母来命名呢？

```
for ($line = 0; $line <= $maxLines, $line++) {
    fwrite($file, $lines[$line]);
} 
```

同样，更显式的名称允许我们更容易和独特地阅读代码。

第三个例子。让我们假设一个简单的算法来匹配体育比赛中的对手:

```
foreach ($teams as $t1) {
    foreach ($teams as $t2) {
        if ($t1 === $t2) {
            continue;
        }
        $matches[] = [$t1, $t2];
    }
} 
```

这没那么难看，但读下面的内容会容易得多:

```
foreach ($teams as $local) {
    foreach ($teams as $visitor) {
        if ($local === $visitor) {
            continue;
        }
        $matches[] = [$local, $visitor];
    }
} 
```

总而言之，改名对我们有好处，因为:

*   它反映了我们对代码的了解
*   它赋予重命名的元素以意义

### 提取常数

每当我们在代码中发现一个数字或一个文本时，就会出现理解它的含义的问题。这个问题是一个叫*幻数*的码味。

幻数(或者更一般的幻值)是代码中引入的任意值，用来表示过去不变的东西。

问题是它们的意义远非显而易见，当我们检查另一个人很久以前写的代码时，特定值的选择可能是一个完全的谜。一般来说，我们可以问两个问题:

*   这个值是什么意思？
*   为什么会被选中？

我们来看一个例子:

```
$this->queryBuilder()->setMaxResults(25); 
```

这里有一个幻数，代表查询必须返回的寄存器的最大数量。更具体地说，它可以指每个 API 页面或视图的元素数量。

```
$this->queryBuilder()->setMaxResults(MAX_ITEMS_PER_VIEW); 
```

另一方面，一旦我们找到了这个问题的答案，另一个问题可能会出现:这些神奇的值是否在所有代码中一致地使用？也就是说:如果这些值被用在代码的其他地方，具有相同的含义，那么我们就有了一个问题，即真理的多个来源可能会出现分歧。

当我们定义一个常数来引用这个神奇的值时，我们可以在多个地方以一致的方式使用它。

```
echo sprintf('showing %s items', MAX_ITEMS_PER_VIEW); 
```

总结一下:从神奇的值中提取常数赋予这些值以意义。

### 提取变量

提取变量的主要动机是简化表达式，将它们分成有意义和可管理的部分。

让我们看一个简单的例子，其中我们应用了一些名称更改:

```
$amount = $price - ($price * $pct / 100);

// vs

$discount = $price * $discountPercent / 100;

$amount = $price - $discount; 
```

我们所做的是提取变量的括号。这有几个好处:

*   它允许使主要的表达更有意义。
*   它有助于我们识别和避免操作符优先级可能出现的问题。

显然这是一个非常简单的例子，但是表达式越复杂，这种重构的好处就越大。

另一个例子:

```
$object->setNewValue($oldValue * 1.3 + $margin);

//vs

$newValue = $oldValue * COEFFICIENT + $margin;
$object->setNewValue($newValue); 
```

将一个表达式提取到一个变量中会使它更加清晰，方法的使用也更加简洁。

### 提取方法

#### 隔离隐藏的依赖关系

当我们阅读 legacy 并发现隐藏的依赖项时，将它们提取到一个方法中是一个很好的第一步，将它从类中取出并在以后注入。

```
// ...
$date = new DateTime();
// ...

// vs

// ...
$date = $this->getCurrentDate();
// ...

public function getCurrentDate()
{
    return new DateTime();
} 
```

当我们提取对一个方法的依赖时，我们将它隔离，并将它的实例化减少到一个。这不是最好的技术，但是使用这种策略，我们可以通过继承创建一个可测试的类，覆盖`getCurrentDate`方法。不过还是再提前一点比较好。

现在，我们可以很容易地用注入的依赖来替换它。

```
 class MyClass
{
    private $clockService;

    public function __construct(ClockService $clockService)
    {
        $this->clockService = $clockService;
    }

    public function getCurrentDate()
    {
        return $this->clockService->currentDate();
    }
} 
```

#### 根据抽象层次简化复杂操作

非常长的方法(超过 20-25 行)可以被分割成紧密相关的代码块，然后被分割成语义统一的更小的方法。

通常，在一个方法的主体中，不同的抽象层次会有不同的关注点。这种混合使得代码难以阅读，因为我们必须不断适应这些级别的变化。

例如，在一行中，我们从一个存储库中获得一些东西，然后我们有十行操作来生成我们需要的一些值。

因此，一个好的策略是用一个有意义的名字将方法中最底层的操作分组，这个名字可以在更高的层次上解释它的作用。如果需要，对提取的方法本身进行同样的操作。

这样，main 方法的主体可以更容易地阅读，给我们一个正在发生的事情的总体概念，并让我们在需要时进入细节。

```
public function doSomething()
{
   // more than 30 lines of code…
}

//vs

public function doSomething()
{
   $this->prepareThings();
   $this->getData();
   $this->processData();
   return $result;
} 
```

好像主体是一本书的索引，不同的章节都是从这里开始的。

让我们看看下面的例子，在这个例子中，我们确保一个值是有效的:

```
if (! in_array($value, self::VALID_TYPES)) {
    //...
    throw new InvalidArgumentException($errorMessage);
}
//... 
```

我们可以用自己的方法提取这一部分:

```
 $this->failIfValueIsAnInvalidType($value);

//...

public function failIfValueIsAnInvalidType(string $value):void
{
    if (! in_array($value, self::VALID_TYPES)) {
        //...
        throw new InvalidArgumentException($errorMessage);
    }
} 
```

#### 封装复杂的操作并赋予其意义

extract 方法的一个特别有用的例子是将它应用于条件表达式，而不仅仅是复杂的表达式，后者的含义并不明显。

从前面的例子中，我们可以使条件表达式更加明确:

```
if (! in_array($value, self::VALID_TYPES)) {
    //...
    throw new InvalidArgumentException($errorMessage);
}
//... 
```

这边走:

```
if (! $this->isValidType($value)) {
    //...
    throw new InvalidArgumentException($errorMessage);
}
//...

public function isValidType(string $value): boolean
{
    return in_array($value, self::VALID_TYPES);
} 
```

### 提取界面

我们提取接口来抽象行为，因此我们可以替换或更改当前执行它们的实现，赋予它们意义。

提取接口允许我们将自己从依赖关系的具体实现中分离出来，或者是因为我们希望能够为他人改变它们，或者是因为我们希望提高测试我们系统的能力，等等。

一个非常简单的例子:我们有一个使用`SomeService`作为依赖的`MyClass`类。

```
class MyClass
{
    private $service;

    public function __construct(SomeService $service)
    {
        $this->service = $service;
    }

    public function doThis(): Class
    {
        return $this->service->doSomething();
    }
}

class SomeService
{
   public function doSomething(): Class
   {
   //...
   }
} 
```

我们根据`MyClass`使用`SomeService`的方式提取一个接口:

```
 interface Service
{
   public function doSomething(): Class;
} 
```

`SomeService`可能有许多其他方法，但我们只对`doSomething`感兴趣。

现在，我们改变`SomeService`来实现刚刚定义的接口，并且我们使`MyClass`期望从接口`SomeService`得到一个依赖。(我们应该更好地创建一个使用`SomeService`的适配器，但是我更喜欢简化这里的例子)。

```
class SomeService implements Service
{
//...
}

class MyClass
{
    private $service;

    public function __construct(Service $service)
    {
        $this->service = $service;
    }
    //...
} 
```

现在，`MyClass`和`SomeService`是解耦的，都唯一依赖于接口`Service`。

如果需要，您可以创建同一接口的新实现:

```
class BetterService implements Service 
```

## 超越写得好的代码

在所有的例子中，重构的好处是更好地反映我们对系统的了解，让代码自己解释。

重构不仅仅是提供给我们一个更好的代码，而是这个过程的结果。重构是关于知识和意义的。

Refactor 让我们将关于系统的知识放在代码本身中，确保概念和操作得到很好的描述，并且领域语言出现在所有代码级别。

由于这个原因，重构也是一项以非常直接的方式使业务受益的任务:业务语言在代码中反映得越好，我们能够在代码中找到的知识就越多，因此改进、修复和新功能将更加高效、快速和安全。