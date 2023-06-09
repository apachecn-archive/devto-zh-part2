# PHP 反射

> 原文：<https://dev.to/mattsparks/php-reflection-47ne>

*最初发布于[发展事件](https://developmentmatt.com/php-reflection/)。*

开始着手分析 PHP 框架，特别是容器，让我有了思考的意识。在此之前，我可能听说过这个术语，但我肯定没有故意使用它。虽然这听起来像一个可怕的计算机科学概念，但它不是。其实很简单:

> 反射是计算机程序检查、反省和修改自身结构的能力...

就是这样。(谢谢，[维基百科](https://en.wikipedia.org/wiki/Reflection_(computer_programming)))

## PHP 中的反射

PHP 提供了一个完整的[反射 API](http://php.net/manual/en/book.reflection.php) 和多个开箱即用的函数。这为对象提供了自我检查和报告的能力，比如它的方法和属性。有了这个功能，我们可以自动创建文档，注入依赖项，等等。

有可能你用过反射，没有意识到。如果您曾经使用过 get_class()或 method_exists()函数，那么您就使用过反射。
PHP 反射的例子

这是我们将要学习的课程:

```
<?php
namespace Springfield\Characters;

class Simpson
{
    private $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }

    public function getName() : string
    {
        return $this->name;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，让我们使用 PHP 的一些内置函数来打印出关于这个类的信息。首先，我们将打印出类方法。

```
$homer = new Simpson('Homer');

var_dump(get_class_methods($homer));

// array(2) { [0]=> string(11) "__construct" [1]=> string(7) "getName" } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们得到类的名字。

```
$homer = new Simpson('Homer');

var_dump(get_class($homer));

// Springfield\Characters\Simpson 
```

Enter fullscreen mode Exit fullscreen mode

正如我上面提到的，PHP 提供了一个完整的 API。PHP 反射类为我们提供了很多强大的工具来检查我们的对象。使用该类很简单，只需实例化该类并传递您想要检查的对象。像这样:

```
$reflection = new \ReflectionClass($homer); 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以得到这个类的属性:

```
var_dump($reflection->getProperties());

// array(1) { [0]=> object(ReflectionProperty)#3 (2) { ["name"]=> string(4) "name" ["class"]=> string(30) "Springfield\Characters\Simpson"  } 
```

Enter fullscreen mode Exit fullscreen mode

或者获取构造函数并了解其参数:

```
$constructor = $reflection->getConstructor();

var_dump($constructor->getParameters());

// array(1) { [0]=> object(ReflectionParameter)#4 (1) { ["name"]=> string(4) "name" } } 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

虽然你可能不会每天都使用反射，但是有时候它会非常方便——例如，使用其他人的代码。我鼓励你去看看下面的延伸阅读部分的链接，以了解更多。

## 进一步阅读

[反射(PHP Docs)](http://php.net/manual/en/book.reflection.php)
[PHP 中的反射是什么？](https://medium.com/tech-tajawal/introduction-to-php-reflection-api-4af07cc17db4)
[PHP 反射 API 简介](https://medium.com/tech-tajawal/introduction-to-php-reflection-api-4af07cc17db4)