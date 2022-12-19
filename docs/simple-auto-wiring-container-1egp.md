# 简单的自动布线容器

> 原文：<https://dev.to/nishimura/simple-auto-wiring-container-1egp>

我不需要阿迪集装箱，但需要自动布线集装箱。
最初，我用自己的框架划分了自己使用的容器类。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ nishphp ](https://github.com/nishphp) / [简单容器](https://github.com/nishphp/simple-container)

### 简单的自动布线容器

<article class="markdown-body entry-content container-lg" itemprop="text">

# 简单容器

[![Build Status](img/769efb5a3f1d46c0b251e437284162e4.png)](https://travis-ci.org/nishphp/simple-container)[![Coverage Status](img/a91d11e019d6eb211caf5b2da4b99620.png)](https://coveralls.io/github/nishphp/simple-container?branch=master)[![Code Climate](img/cbe55d3409c68e23cf903962dd02a6b5.png)](https://codeclimate.com/github/nishphp/simple-container)

[![Latest Stable Version](img/f8f03f5d03ff1f50b9c38be78e301a6f.png) ](https://packagist.org/packages/nish/simple-container) [ ![License](img/c943daf4bd14ca19aaa129cc520d535d.png)](https://raw.githubusercontent.com/nishphp/simple-container/master/LICENSE)

带有构造函数的自动布线的简单容器。

```
<?php
use Nish\Container\Container
require_once 'vendor/autoload.php'
$c = Container::getInstance()
$obj = $c->get(stdClass::class)
$c->call(Foo::class, 'staticMethod');

$foo = new Foo();
$c->call($foo, 'method');

$c->setFactory('MyClass', function($c){
    return new MyClass('custom param');
});
$c->setFactory(MyClass::class, function($c){
    $obj = new MyClass('custom param');
    $c->set(MyClass::class, $obj); // singleton
    return $obj;
});

// set arguments
namespace MyProject;
class Db {
    private $dsn;
    public function __construct(string $dsn){
        $this->dsn = $dsn;
    }
    // ...
}

```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/nishphp/simple-container)

这个容器类只有我在项目中实际使用的基本方法。

接口的实现由`set`方法注册，依赖关系由 PHP 代码写到`setFactory`方法。
然而，大多数依赖将通过自动布线解决。

```
class Foo
{
    private $obj;
    public function __construct(stdClass $obj)
    {
        $this->obj = $obj;
    }
}

$c = Container::getInstance();
$foo = $c->get(Foo::class); // constructor injection, no config

// set implements
interface Uri {}
class MyUri implements Uri {}

$c->set(Uri::class, new MyUri);
$uri = $c->get(Uri::class);

// or
$c->setFactory(Uri::class, function($c){
    return new MyUri();
});

// singleton
$c->setFactory(Uri::class, function($c){
    $myUri = new MyUri();
    $c->set(Uri::class, $myUri);
    return $myUri;
}); 
```

Enter fullscreen mode Exit fullscreen mode

当在方法调用中使用自动连接时，您需要手动调用`call`方法。

```
class MyIndexAction
{
    public static function index(Request $req, Response $res, MyForm $myForm)
    {
        // ...
    }

    public function __construct(MyForm $myForm) { $this->myForm = $myForm; }
    public function index2(Request $req, Response $res)
    {
        // ...
    }
}

$c->call(MyIndexAction::class, 'index');

// or
$action = $c->get(MyIndexAction::class);
$c->call($action, 'index2'); 
```

Enter fullscreen mode Exit fullscreen mode

这些示例中解释了该容器的大多数功能。
简单，但当与`FastRoute`、会话库等结合使用时。，一个简单的微框架瞬间就完成了。

注:我不知道这个程序是否可以在实际项目中使用。我正在尝试。