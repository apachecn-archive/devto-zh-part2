# 构建 PHP 框架:第 7 部分——容器

> 原文：<https://dev.to/mattsparks/building-a-php-framework-part-7---the-container-1edc>

*[原贴 DevelopmentMatt.com](http://developmentmatt.com/building-a-php-framework-part-7-the-container/)T3】*

[第 6 部分](http://developmentmatt.com/building-a-php-framework-part-6-dependency-inversion-inversion-of-control-oh-my/)开始了我们对 PHP 容器的讨论。今天，我将更详细地介绍这个主题，同时，我将介绍到目前为止在分析容器上所做的工作。

对于 PHP 联盟容器和其他人的幕后工作人员，我们感激不尽。研究他们的代码我学到了很多。

## 快速回顾

什么是容器，我们为什么要使用它们？容器既是由对象组成的注册表，也是检索它们的机制。可以说是图书馆和图书管理员。容器为开发人员提供了更容易管理依赖关系的工具。

## 分析容器

在分析框架中使用的容器的工作已经开始，在撰写本文时，基础已经基本完成。请记住，它还没有完成，但已经足够进行讨论了。

容器的所有代码都可以在 Github 上找到。

### 一个简单的例子

目前，有两种方法向容器注册对象。第一种是通过使用工厂。工厂在检索时使用闭包来构建对象。

```
$container = new Container;

$container->addFactory('Carbon', function () {
    return new \Carbon\Carbon;
});

$carbon = $container->get('Carbon');

var_dump($carbon->year) // 2018 
```

Enter fullscreen mode Exit fullscreen mode

第二种选择是使用完全限定名注册一个类。

```
$container = new Container;

$container->addClass('Carbon', '\Carbon\Carbon');

$carbon = $container->get('Carbon');

var_dump($carbon->year) // 2018 
```

Enter fullscreen mode Exit fullscreen mode

这两种方法都将别名作为第一个参数。

## 容器如何工作

在其最基本的核心，容器只是一个带有参数的对象——一个保存对象定义的数组。当我们请求一个依赖项(`get('Carbon')`)时，容器在那个数组中寻找它。如果找到了，就构建并返回该对象。否则，将引发异常。确切地说，是一个`NotFoundException`异常。

```
<?php 

[...]

class Container implements ContainerInterface
{

    /**
     * Definitions
     * @var array
     */
    private $definitions = [];

    [...]

    /**
     * Get
     *
     * Returns the passed alias.
     *
     * @param  string $id
     * @return mixed
     * @throws NotFoundException
     */
    public function get($id)
    {
        if (!$this->has($id)) {
            throw new NotFoundException(sprintf('%s is not defined.', $id));
        }

        $definition = $this->definitions[$id];
        $definition->addArguments($this->arguments);

        return $definition->build();
    }

    [...]

} 
```

Enter fullscreen mode Exit fullscreen mode

### 定义

定义是容器最重要的方面。它们定义了如何构建对象，如果没有这些功能，拥有容器就没有任何意义。每个定义都必须实现`DefinitionInterface`。

```
interface DefinitionInterface
{
    /**
     * Build
     *
     * @return object
     */
    public function build();
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着，每个定义必须有一个返回对象的`build`方法。

每个定义都应该扩展`AbstractDefinition`类，但技术上并不要求这样。

```
abstract class AbstractDefinition implements DefinitionInterface
{
    /**
     * Arguments
     * @var array
     */
    public $arguments = [];

    /**
     * Add Arguments
     *
     * @param array $args
     */
    public function addArguments(array $args) : void
    {
        $this->arguments = $args;
    }

    /**
     * Has Arguments
     *
     * @return bool
     */
    public function hasArguments() : bool
    {
        return count($this->arguments) > 0;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该类提供跨定义使用的属性和方法。

```
class FactoryDefinition extends AbstractDefinition
{
    /**
     * Callback
     * @var Closure
     */
    private $callback;

    /**
     * Constructor
     *
     * @param Closure $callback
     */
    public function __construct(Closure $callback)
    {
        $this->callback = $callback;
    }

    /**
     * Build
     *
     * @return object
     */
    public function build()
    {
        return call_user_func_array($this->callback, $this->arguments);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class ClassDefinition extends AbstractDefinition
{
    /**
     * Concrete
     * @var string
     */
    private $concrete;

    /**
     * Constructor
     *
     * @param string $concrete
     */
    public function __construct(string $concrete)
    {
        $this->concrete = $concrete;
    }

    /**
     * Build
     *
     * @return object
     */
    public function build()
    {
        if ($this->hasArguments()) {
            $reflection = new ReflectionClass($this->concrete);

            return $reflection->newInstanceArgs($this->arguments);
        }

        return new $this->concrete;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这两种类型的定义都描述了如何构建一个对象。

### 论据

我相信你已经注意到在上面的代码中，在构建一个对象时可以使用参数。例如，假设您可以在构建 Carbon 实例时传递日期和时间。这看起来应该是这样的:

```
$container->withArguments(['2010-05-16 22:40:10'])->get('Carbon'); 
```

Enter fullscreen mode Exit fullscreen mode

## 还剩下什么可做的

在容器方面还有相当多的工作要做，但是我对现在的情况很满意。剩下的最紧迫的特性是实现一个`SetterDefinition`，以及与之相关的调用方法的能力。我还将使用反射来自动注入依赖项。我不完全确定那会是什么样子，但是我很兴奋开始着手做这件事。

## 反馈

我很乐意收到你的来信。如果你有建议或其他什么，请打我的 [Twitter 机器](https://twitter.com/DevelopmentMatt)联系我。

最后一件事，请务必查看我的时事通讯！每周我都会给你发一封很棒的电子邮件，里面有更新、很棒的链接、提示&技巧和其他非开发人员的随机信息。如果你感兴趣，你可以通过点击这个[链接](http://eepurl.com/dvwlM5)来注册。