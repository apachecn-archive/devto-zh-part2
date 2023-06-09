# PHP 类型声明的实际工作方式

> 原文：<https://dev.to/robdwaller/how-php-type-declarations-actually-work-1mm5>

类型声明是一个简单的编程概念，许多开发人员每天都在使用。在下面的[代码示例中，我们看到了一个使用类型声明的基本方法。该方法在`$foo`参数上放置一个字符串类型提示，在方法本身上放置一个字符串返回类型。](https://3v4l.org/PJOaL) 

```
function bar(string $foo): string
{
    return $foo;
}

$result = bar('Hello');

var_dump($result);

// Output: string(5) "Hello" 
```

这些[类型声明](https://en.wikipedia.org/wiki/Declaration_(computer_programming))告诉我们关于这个方法的两件事。它接受一个字符串参数，并将返回一个字符串。在像 Go 这样的严格类型语言中，如果调用了`bar()`方法并且传入了一个整数参数，比如 123，就会发生错误。如果该方法由于某种原因返回字符串以外的内容，代码也会出错。

类型声明在代码中有两个基本用途:

*   **低级数据/代码完整性:**误用方法更加困难，减少了类型检查。
*   **代码可读性:**更清楚一个方法接受什么，返回什么。

PHP 在历史上一直是一种弱类型语言。类型声明只在 PHP 5 中引入，而且是以有限的形式。完整类型声明和严格类型直到 2015 年 PHP 7 的发布才出现。

对于我们这些喜欢类型声明和严格类型代码的人来说，PHP 7 是一个令人兴奋的进步。尽管有两个重要的警告，PHP 开发人员应该知道。

## PHP 7 是一种弱类型语言

默认情况下，PHP 7 仍然是弱类型语言。考虑到完整类型声明和严格类型的引入，这可能看起来违反直觉。但是为了帮助向后兼容，决定不在 PHP 代码中强制类型声明。开发人员可以通过将`declare(strict_types=1);`方法放在 PHP 文件的顶部来打开严格类型。

这种实现意味着 PHP 将“忽略”类型提示并返回类型，除非`declare(strict_types=1);`语句出现在文件的顶部。这很有意义，因为仍有大量遗留的 PHP 代码在使用。开发人员应该能够升级到 PHP 的最新版本，而无需重写他们的整个代码库。

不过这种方法确实有一些后果。如果开发人员定义了类型声明，但没有添加`declare(strict_types=1);`，PHP 将使用类型强制来实现。类型强制基本上意味着一种类型的值将在需要时被转换为另一种类型的值。当使用类型声明时，这并不理想，因为方法可能无法按预期运行。

作为[的一个例子](https://3v4l.org/EGpLn)，如果我们从上面编辑我们的‘foo bar’代码，我们会看到类型强制在起作用。bar 方法现在在参数`$foo`上有一个整数类型提示，在方法上有一个字符串返回类型。当我们使用整数参数 123 调用 bar 方法时，该方法将以字符串形式返回 123。这是因为类型强制将整数值转换为字符串值，以符合定义的返回类型。

```
function bar(int $foo): string
{
    return $foo;
}

$result = bar(123);

var_dump($result);

// Output: string(3) "123" 
```

只有当我们将`declare(strict_types=1);`添加到 PHP 文件的顶部[时，类型声明才会生效。](https://3v4l.org/dtYQ9) 

```
declare(strict_types=1);

function bar(int $foo): string
{
    return $foo;
}

bar(123); 
```

现在，当我们使用整数参数 123 调用 bar 方法时，我们会得到一个错误。这是因为该方法预期返回一个字符串，但它试图返回一个整数。对该方法施加了严格的类型，并输出以下错误。

```
FATAL ERROR Uncaught TypeError: Return value of bar() must be of the type string, integer returned 
```

## 一个文件一个文件地施加严格的类型

需要注意的另一个警告是，严格类型必须逐个文件地施加，它们不能全局应用。这意味着您必须将`declare(strict_types=1);`放在每个想要施加严格类型的文件中。同样，这样做是为了帮助向后兼容，但这意味着您不能轻易地在应用程序中强加严格的类型。

为了展示这一点，让我们看看另一个例子[。假设我们有一个小应用程序，它从两个独立的文件`foo.php`和`bar.php`中提取两个函数。我们将在应用程序文件的顶部添加一个`declare(strict_types=1);`,只是为了证明它对读入的文件没有影响。此外，我们将把我们调用的方法放在 try catch 中，以处理任何抛出的错误。](https://github.com/RobDWaller/type-declarations) 

```
/**
 * This declares strict types but has no effect on the code.
 */
declare(strict_types=1);

/**
 * Call in the foo.php and bar.php files.
 */
require __DIR__ . '/../src/foo.php';
require __DIR__ . '/../src/bar.php';

try {
    /**
     * This method will output a string 123.
     */
    var_dump(foo(123));

    /**
     * This method will throw an error.
     */
    var_dump(bar(123));

} catch (Error $e) {

    /**
     * Print out the error message thrown by the bar(123) method
     * "Return value of bar() must be of the type string, integer returned"
     */
    echo $e->getMessage() . PHP_EOL;
} 
```

在 foo 文件中，我们将有一个 foo 方法，它接受一个整数并返回一个字符串。在这个文件中没有`declare(strict_types=1);`语句。

```
function foo(int $bar): string
{
    return $bar;
} 
```

在 bar 文件中，我们将有一个 bar 方法，它也接受一个整数并返回一个字符串。不过这一次我们将在文件的顶部有一个`declare(strict_types=1);`。

```
declare(strict_types=1);

function bar(int $foo): string
{
    return $foo;
} 
```

当我们分别调用`foo(123)`和`bar(123)`方法时，我们会得到一些非常不同的输出。foo 方法将返回一个字符串，所以我们将看到`string(3) "123"`的输出。相比之下，bar 方法将抛出一个错误，因为已经强加了严格类型。我们将看到一条错误消息输出，`Return value of bar() must be of the type string, integer returned`。这个例子说明了在同一个应用程序中，严格类型可以同时打开和关闭。

## 后果

我不想讨论 PHP 的类型声明方法的对错，比我更聪明的人已经设计并实现了它们。但是这种方法的后果应该是显而易见的。最重要的是 PHP 的类型系统不可信。这意味着低级的完整性错误可能会溜进代码中，除非你小心使用`declare(strict_types=1);`语句。

这也意味着您在测试代码时应该小心。例如，当使用 [PHPUnit](https://phpunit.de/manual/6.5/en/appendixes.assertions.html#appendixes.assertions.assertSame) 进行单元测试时，你应该使用`assertSame()`而不是`assertEquals()`。这是因为`assertSame()`检查值和类型，而`assertEquals()`只检查值。如果你想确保代码的完整性，你需要检查类型和值。

类型声明是 PHP 向前迈出的一大步，我相信无论`declare(strict_types=1);`是开还是关，所有的 PHP 开发人员都应该使用它们。PHP 开发人员了解他们是如何工作的是很重要的，因为误解 PHP 的类型系统会导致错误，并对应用程序产生负面影响。

关于 PHP 类型系统的更多信息，我建议你[阅读关于 PHP 标量类型声明的 RFC](https://wiki.php.net/rfc/scalar_type_hints_v5) 。