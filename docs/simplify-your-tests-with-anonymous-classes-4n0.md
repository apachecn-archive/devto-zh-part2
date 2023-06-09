# 用匿名类简化你的测试

> 原文：<https://dev.to/matthewbdaly/simplify-your-tests-with-anonymous-classes-4n0>

PHP7 中加入了匿名类，但是到目前为止我还没有充分利用它们。然而，最近我一直在为学习目的构建一个简单的依赖注入容器。这使用 PHP 反射 API 来确定如何解决依赖关系。例如，如果要求一个类，其构造函数所需的依赖项之一是`DateTime`类的实例，那么它应该创建一个实例，然后在实例化该类时自动将其传递给构造函数。那么它应该返回新创建的类。

模仿并不真正适合这个用例，因为容器需要返回一个具体的类实例来正确地完成它的工作。您可以创建一系列纯粹用于测试目的的 fixture 类，但这意味着要么在一个文件中定义多个类(违反 PSR-2)，要么在单独的文件中定义大量的 fixture 类，这意味着您必须编写大量的样板文件，并且您必须在几个不同的文件之间移动以理解测试中发生的事情。

匿名类允许您为内联测试编写简单的类，就像在这个例子中检索一个非常基本的类一样。测试使用 PHPSpec:

```
<?php

namespace spec\Vendor\Package;

use Vendor\Package\MyClass;
use PhpSpec\ObjectBehavior;
use Prophecy\Argument;
use DateTime;

class MyClassSpec extends ObjectBehavior
{
    function it_can_resolve_registered_dependencies()
    {
        $toResolve = new class {
        };
        $this->set('Foo\Bar', $toResolve);
        $this->get('Foo\Bar')->shouldReturnAnInstanceOf($toResolve);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以内联定义自己的方法。这里我们实现了`invoke()`魔法方法，这样这个类就是一个可调用的:

```
<?php

class MyClassSpec extends ObjectBehavior
{
    function it_can_resolve_registered_invokable()
    {
        $toResolve = new class {
            public function __invoke() {
                return new DateTime;
            }
        };
        $this->set('Foo\Bar', $toResolve);
        $this->get('Foo\Bar')->shouldReturnAnInstanceOf('DateTime');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以定义一个构造函数。这里，我们正在获取一个新创建的匿名类的类名，该类接受一个实例`DateTime`作为构造函数的参数。然后，我们可以从容器中解析出一个新的实例:

```
<?php

class MyClassSpec extends ObjectBehavior
{
    function it_can_resolve_dependencies()
    {
        $toResolve = get_class(new class(new DateTime) {
            public $datetime;

            public function __construct(DateTime $datetime)
            {
                $this->datetime = $datetime;
            }
        });
        $this->set('Foo\Bar', $toResolve);
        $this->get('Foo\Bar')->shouldReturnAnInstanceOf($toResolve);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于将扩展现有类或实现接口的类，也可以内联定义它们。或者你可以包含一个特质:

```
<?php

class MyClassSpec extends ObjectBehavior
{
    function it_can_resolve_dependencies()
    {
        $toResolve = get_class(new class(new DateTime) extends Foo implements Bar {
            public $datetime;

            public function __construct(DateTime $datetime)
            {
                $this->datetime = $datetime;
            }

            use MyTrait;
        });
        $this->set('Foo\Bar', $toResolve);
        $this->get('Foo\Bar')->shouldReturnAnInstanceOf($toResolve);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在功能包含在 trait 或抽象类中的情况下，您可能需要添加很少或不需要添加额外的功能，这比用传统方式创建类要简单得多。

没有匿名类，所有这些都是不能做的，但是通过在测试中内联定义这种一次性的 fixture 类，您编写了实现测试所需的最少代码，并且内联定义它是合乎逻辑的，因为它只在测试中使用过。有一点要记住，匿名类是同时创建和实例化的，所以你不能简单地创建一个类，然后单独实例化它的一个实例。然而，您可以实例化一个，然后使用`get_class()`函数获取它的类名，并使用它来解析它，这对于我的用例来说很好。

匿名类的另一个用例是测试特征或抽象类。我通常使用 mocking 作为 PHPUnit 测试的模拟解决方案，但是我有时会错过 PHPUnit 的`getMockForTrait()`方法。然而，另一种选择是实例化一个匿名类，该匿名类包含用于测试目的的特征:

```
<?php

$item = new class() {
    use MyTrait;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这样，您的测试类尽可能的少，并且您可以以一种相当隔离的方式测试 trait/abstract 类。