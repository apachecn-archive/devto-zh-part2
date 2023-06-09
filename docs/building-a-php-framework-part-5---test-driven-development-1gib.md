# 构建 PHP 框架:第 5 部分——测试驱动开发

> 原文：<https://dev.to/mattsparks/building-a-php-framework-part-5---test-driven-development-1gib>

在第四部分中，我们为分析奠定了基础。现在是时候写第一行代码了！

在我们继续之前，请允许我插一句话:我已经开始了一份时事通讯！每周我都会给你发一封很棒的电子邮件，里面有更新、很棒的链接、提示&技巧和其他非开发人员的随机信息。如果你感兴趣，你可以通过点击这个[链接](http://eepurl.com/dvwlM5)来注册。

## 什么是测试驱动开发？

让我们把维基百科的定义放在一边:

> 测试驱动开发(TDD)是一个软件开发过程，它依赖于一个非常短的开发周期的重复:需求被转化为非常具体的测试用例，然后软件被改进以通过新的测试。这与允许添加未被证明满足需求的软件的软件开发相反。

太好了。这意味着什么呢？

本质上，测试驱动开发是一个在编写任何代码之前编写测试，然后编写通过测试的代码的过程。

1.  书写测试
2.  运行测试，发现新测试失败
3.  编写通过测试的代码
4.  运行测试
5.  重构

如果您是 TDD 的新手，这可能看起来很奇怪。跟着我。

## 测试驱动开发的工具

我将使用测试框架 [PHPUnit](https://phpunit.de/) 来完成我所有的测试。关于如何安装 PHPUnit 的说明，请参见他们的[文档](https://phpunit.readthedocs.io/en/7.1/installation.html)。

注意:在这篇文章中，我仅仅触及了 PHPUnit 的皮毛。我强烈建议您通读文档。

## 一个简单的例子

假设您想要构建一个接受两个数并将它们相加的类。让我们编写一个测试来确保我们的 add 方法返回正确的结果。请记住，这个例子最初只是简单地说明 PHPUnit 在基本层面上是如何工作的。

```
<?php
use PHPUnit\Framework\TestCase;

class ExampleTest extends TestCase
{
    public function testTwoNumbersAreAdded()
    {
        $adder = new NumberAdder(1, 2);
        $result = $adder->add();

        $this->assertEquals(3, $result);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个测试中，我们创建了一个新的`NumberAdder`对象，传递我们想要操作的两个数字(1 和 2)。从那里，我们将这些数字加在一起，并告诉 PHPUnit 我们期望结果等于 3。让我们运行它:

[![TDD Example 1](img/d80c3650cf4861f18fb26cbd006311bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zstGiCIg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://developmentmatt.com/wp-content/uploads/2018/05/tdd_example_1.png)

当然，它没有给我们:

```
Error: Class 'NumberAdder' not found 
```

Enter fullscreen mode Exit fullscreen mode

按照 TDD 方法，让我们创建类并消除这个错误。

```
<?php
namespace NumberAdder;

class NumberAdder
{

} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，将其导入到我们的示例 Test:

```
use NumberAdder\NumberAdder; 
```

Enter fullscreen mode Exit fullscreen mode

现在再运行一次:

[![TDD Example 2](img/58d17f78999a09d0339c09a404efa0a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x43nADeF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://developmentmatt.com/wp-content/uploads/2018/05/tdd_example_2.png)

很好，错误消失了，我们有了一个新的:

```
Error: Call to undefined method NumberAdder\NumberAdder::add() 
```

Enter fullscreen mode Exit fullscreen mode

有道理，我们还没有创造它。就这么办吧。

```
<?php
namespace NumberAdder;

class NumberAdder
{
    public $firstNumber;
    public $secondNumber;

    public function __construct(int $firstNumber, int $secondNumber)
    {
        $this->firstNumber = $firstNumber;
        $this->secondNumber = $secondNumber;
    }

    public function add() : int
    {

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![TDD Example 3](img/63220acd3c8c2df224b6f965fd70593a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XKSQEVaj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://developmentmatt.com/wp-content/uploads/2018/05/tdd_example_3.png)

好了，之前的错误没了。现在我们得到:

```
Failed asserting that null matches expected 3. 
```

Enter fullscreen mode Exit fullscreen mode

我们的 add 方法不返回任何东西，因此是 null，而 null 不等于 3。最后一次修正:

```
<?php
namespace NumberAdder;

class NumberAdder
{
    public $firstNumber;
    public $secondNumber;

    public function __construct(int $firstNumber, int $secondNumber)
    {
        $this->firstNumber = $firstNumber;
        $this->secondNumber = $secondNumber;
    }

    public function add() : int
    {
        return $this->firstNumber + $this->secondNumber;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![TDD Example 4](img/25f65486822642b9387a335fe799a513.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z6cuxFBt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://developmentmatt.com/wp-content/uploads/2018/05/tdd_example_4.png)

它过去了！

## 第一次分析测试

前面的例子是 TDD 过程的一个非常简化的纲要。写一个测试，运行，它失败了，让它不要失败，再次运行，重复。我将在构建 Analyze PHP 框架的整个过程中一直这样做。下面是第一个:

```
<?php
use PHPUnit\Framework\TestCase;
use Analyze\Application;

class ApplicationTest extends TestCase
{
    public function testApplicationInstanceIsReturned()
    {
        $app = new Application;

        $this->assertInstanceOf(Application::class, $app);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在路上了！确保跟踪[框架库](https://github.com/AnalyzePHP/framework)。

[最初发布于 DevelopmentMatt.com](https://developmentmatt.com/building-a-php-framework-part-5-test-driven-development/)