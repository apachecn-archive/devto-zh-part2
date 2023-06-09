# ★在实例变量中调用可调用的

> 原文：<https://dev.to/freekmurze/calling-an-invokable-in-an-instance-variable-4n99>

PHP 中的可调用类是可以作为函数使用的类。它们从 PHP 5.3 开始就存在了，并且有许多 [有趣的](https://laravel.com/docs/master/scheduling#defining-schedules) [使用](https://symfony.com/doc/current/controller/service.html#invokable-controllers) [案例](https://laracasts.com/series/php-bits/episodes/6)。这里有一个简单的例子。

```
class Invokable
{
    public function __invoke()
    {
        echo 'I have been invoked';
    }
} 
```

可以这样用:

```
// outputs 'I have been invoked'
$invokable(); 
```

不错！现在让我们使用一个 invokable 作为实例变量。考虑一下这段代码，我们定义了一个新的类，它有一个可调用的实例变量:

```
class Foo
{
    /** \Invokable */
    protected $invokable;

    public function __construct(Invokable $invokable)
    {
       $this->invokable = $invokable;
    }

    public function callInvokable()
    {
        // let's implement this
    }
} 
```

让我们看看如何实现这个`callInvokable`函数。你的第一直觉可能是这样做:

```
public function callInvokable()
{
   $this->invokable();
} 
```

这会爆炸，因为这段代码试图在`Foo`上调用一个不存在的函数`invokable` function。

要做好这件事，你有三个选择:

```
public function callInvokable()
{
    // first option: nice
    call_user_func($this->invokable);

    // second option: ok too
    $this->invokable->__invoke();

    // third option (only avaible for PHP 7): ?
    ($this->invokable)();
} 
```