# 从 PHPUnit 中的 PHPT 测试开始测试

> 原文：<https://dev.to/moxio/start-testing-with-phpt-tests-in-phpunit-2jpg>

多年来，自动化测试已经成为软件开发中的一种既定实践。因此，对于任何开发人员来说，这也是一项必须学习的技能。在过去的几个月里，我和几个最近开始用 PHP 进行测试的开发人员交谈过。他们中的一些人表示，他们发现他们选择的测试框架(通常是 [PHPUnit](https://phpunit.de/) )很难开始使用。

从某种意义上说，这并不奇怪。开始测试本身已经很有挑战性了。您必须选择要测试的单元，并学习编写可测试的代码。您必须学会区分基本行为和实现细节。同时，你必须了解你的测试框架。大多数流行和成熟的测试工具需要一些样板文件和背景知识。这增加了本已陡峭的学习曲线。由于人们通过一步一步地学习效果最好，这并不理想。如果我们有一个简单的测试框架，让低年级学生不用额外的开销就可以学习测试的基本知识，那就更好了。

### PHPT 测试

输入 PHPT 测试。通过修复 PHP 核心中的一些小错误(以及[谈到那个](https://speakerdeck.com/aboks/getting-started-with-php-core-development-php-serbia-2018))，我了解了 PHPT 测试格式。这是用于测试 PHP 解释器本身的测试格式。它简单明了。主要思想是，一个测试包含一个打印输出的 PHP 脚本，以及该脚本的预期输出。一个简单的 PHPT 测试应该是这样的:

```
--TEST--
Basic arithmetic - addition
--FILE--
<?php var_dump(42 + 1); ?>
--EXPECT--
int(43) 
```

Enter fullscreen mode Exit fullscreen mode

这里，`--TEST--`部分提供了关于测试目的的简短描述。`--FILE--`部分包含一个打印一些输出的脚本。在这种情况下，我们输出两个整数相加的结果。在`--EXPECT--`之后是我们期望从`--FILE--`部分得到的输出。

注意，我们使用`var_dump`来输出结果值。如果我们使用 echo 或 print，输出将只是`43`。在这种情况下，我们不知道这是整数 43 还是字符串“43”。这使得我们无法验证结果的类型是否正确。因此我们更喜欢`var_dump`:它显示了值的类型。

PHPT 测试格式功能不丰富。尽管如此，它仍然支持编写合理测试所需的所有结构。您可以通过添加以下部分来使用它们:

*   `--EXPECTF--`、`--EXPECTREGEX--`:用户可以指定期望输出的模式，而不是用`--EXPECT--`指定确切的期望输出。这种模式可以作为类似 printf 的字符串或正则表达式提供。
*   您可以添加此部分来描述何时应该跳过测试。这可以用来检查特定平台或所需 PHP 扩展的存在。
*   当测试创建了一些临时工件(比如磁盘上的文件)时，本节中的代码可以清理它们。
*   `--INI--`，`--ENV--`:有时候测试需要使用特定的设置来运行，比如 php.ini 指令或者环境变量。您可以在这两个块中指定这些设置。

关于 PHPT 测试结构的更多信息可以在 PHP QA 团队的[网站上找到。](https://qa.php.net/phpt_details.php)

### 跟 PHPT 一起学习考试

在学习测试时，我们可以看到 PHPT 的节俭特性是一个优势。它允许人们专注于学习测试和可测试性的概念，而不会迷失在特定测试框架的特性中。这些特性对于已经习惯于测试的开发人员非常有用。不过，最好还是先了解一下它们是如何工作的。事实上，低年级学生可能会用他们已经知道的概念找到一种“幼稚”的方式来模仿他们:

*   想测试一个函数抛出异常？用一个`try` - `catch`。还不需要`expectException()`。
*   有多个看起来相似但数据不同的测试？将它们放入`for`或`foreach`循环中。这为以后的数据提供者铺平了道路。
*   在测试之间共享一些初始化逻辑？把它移到一个函数中。后来，这样的函数可以变成一个`setUp`-方法。
*   需要测试替身吗？自己在测试中创建一个虚拟实现。这有助于了解嘲讽库在幕后做什么。它还鼓励保持接口小，方法链短。不同类型的测试替身的模式将会更自然地出现。我们不再让他们都来自`createMock()`了。这有助于低年级学生了解 stub 和 mock 的不同之处。

另外，PHPT 测试使得编写[特性测试](https://michaelfeathers.silvrback.com/characterization-testing)变得容易。以某种方式测试系统的任何代码片段，并将其放入测试中。然后运行它以获得当前输出，并将其注册为预期输出。这个策略提供了一个很好的机会来学习如何使用特性测试来帮助重构遗留代码。它还告诉我们，它们是多么脆弱，最终应该被正确指定所需行为的测试所取代。

### 使用 PHPUnit 进行 PHPT 测试

PHPUnit 的一个鲜为人知的特性是，它实际上内置了对开箱即用的 PHPT 测试的支持。我们所要做的就是在`phpunit.xml`配置文件中添加一个带有`.phpt`后缀的目录:

```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit bootstrap="vendor/autoload.php">
    <testsuites>
        <testsuite name="My Test Suite">
            <directory>test</directory>
            <!-- (line below added) -->
            <directory suffix=".phpt">test</directory>
        </testsuite>
    </testsuites>
    <!-- ... -->
</phpunit> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们这样做了，PHPUnit 还会在`test`目录中寻找`.phpt`文件，并像 PHPT 测试一样执行它们。有了这个配置，我们就可以开始混合“正常的”PHPUnit 测试和 PHPT 测试。我们仍然可以运行它们，就像没有区别一样。我们只是同时支持两种测试格式，不需要任何额外的基础设施或工具。

这意味着有测试经验的开发人员仍然可以编写他们常用的 PHPUnit 测试。最近开始测试的开发人员现在有了一种更简单的编写测试的方法。代替下面的测试来自 [PHPUnit 手册](https://phpunit.readthedocs.io/en/7.1/writing-tests-for-phpunit.html)

```
<?php
use PHPUnit\Framework\TestCase;

class StackTest extends TestCase {
    public function testPushAndPop() {
        $stack = [];
        $this->assertSame(0, count($stack));

        array_push($stack, 'foo');
        $this->assertSame('foo', $stack[count($stack)-1]);
        $this->assertSame(1, count($stack));

        $this->assertSame('foo', array_pop($stack));
        $this->assertSame(0, count($stack));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以写这个等价的 PHPT 测试:

```
--TEST--
Array as a stack
--FILE--
<?php
$stack = [];
var_dump(count($stack));

array_push($stack, 'foo');
var_dump($stack[count($stack)-1]);
var_dump(count($stack));

var_dump(array_pop($stack));
var_dump(count($stack)); 
?>
--EXPECT--
int(0)
string(3) "foo"
int(1)
string(3) "foo"
int(0) 
```

Enter fullscreen mode Exit fullscreen mode

请注意样板文件/先决条件中的差异，以便理解这两个测试。对于 PHPUnit 测试，您需要了解 PHPUnit `TestCase`类，并对 OOP 有一个基本的了解。您还必须知道，以`test`开头的方法是由 PHPUnit 测试运行程序调用的。最后，您需要能够选择合适的断言方法。

通过 PHPT 测试，初级用户可以获取任何现有的产生一些输出的 PHP 代码片段，并将其转化为自动化测试。他们可能已经使用这样的脚本进行手工测试。现在，他们可以将它转换成一个与测试套件的其余部分一起运行的测试。

### 局限性

当然，我们大多数人并不使用 PHPT 编写测试，而是使用像 PHPUnit、 [phpspec](https://www.phpspec.net/) 或 [atoum](http://atoum.org/) 这样的框架。这是有充分理由的。使用 PHPT 格式并通过 PHPUnit 运行它有一些严重的限制:

*   特色。大多数测试框架都内置了一些有用的特性。这些有助于减少为某些测试编写的代码量，或者澄清某些测试失败的原因。想想数据提供者和与 mocking 库的集成。但是也有测试依赖、预期异常的方式和专门的断言方法。我们已经讨论过，这些中的大多数都可以在测试中被“模拟”。尽管如此，在我们的测试框架中包含这些内容减少了我们需要编写的样板文件的数量。这使得有经验的测试人员更有效率。
*   IDE 支持。大多数 IDE 都不支持 PHPT 测试。通过正确的文件关联，标签之间的代码通常仍然会被高亮显示。不过，PHPT 区不会。此外，我不知道有哪种 IDE 可以快速运行单个`.phpt`文件作为编辑器的 PHPT 测试。
*   不支持的部分。PHPUnit 测试运行程序对 PHPT 测试的支持有限。这个 runner 不支持几个 PHPT 部分，其中大部分与模拟 HTTP 输入有关。

### 结论

如果你已经有编写测试的经验，我看不出转换到 PHPT 测试有什么用。尽管如此，对于经验不足的开发人员来说，使用 PHPT 测试可能是开始测试的好方法。有了很少的样板文件和很少的特性集，它可以帮助他们专注于测试和编写可测试代码的方式。这本身就够困难的了，值得他们全力关注。更高级的特性和构造会自然出现，并有助于发现测试框架是如何工作的。

最重要的是:在 PHPUnit 中，只需一行配置，PHPT 测试就可以和“普通”测试一起进行。这样的策略提供了从基本 PHPT 测试到更成熟的测试框架的平稳升级路径。它甚至不需要额外的工具。帮助您的开发人员开始测试，启用 PHPT 测试！

* * *

##### 喜欢看这个帖子？

我和我的同事们定期写类似这样的与 webdev 相关的博客帖子来分享我们学到或发现的东西。你可以在 [Moxio 博客](https://www.moxio.com/blog)上找到我们所有的帖子。通过在 Twitter 上关注 [me](https://twitter.com/arnoutboks) 或 [Moxio](https://twitter.com/moxio) 或订阅我们的 [RSS 源](https://www.moxio.com/rss/blog)来获得新帖子的通知。