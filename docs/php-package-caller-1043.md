# PHP 包:调用者

> 原文：<https://dev.to/devmazee2057282/php-package-caller-1043>

## ![](../Images/375dfcc32199b4dedf2b526645c27ff7.png) [福伯](https://github.com/fobber) / [来电者](https://github.com/fobber/caller)

### 用于调用函数、闭包、类和方法的库。

<article class="markdown-body entry-content" itemprop="text">

# 关于

这是一个可以用来调用函数、闭包(又名:匿名函数)、类和方法的库。它可以用于调用动态值，比如 HTTP 路由器中的处理程序。或者容器。

## 装置

您可以通过 composer 安装，或者手动下载文件。

```
composer require fobber/caller 
```

### 呼叫者文档

```
\Fobber\Caller\Caller::callClosure(
 \Closure $closure,
 array    $parameters = []
);
\Fobber\Caller\Caller::callFunction(
 string $function,
 array  $parameters = []
);
\Fobber\Caller\Caller::callClass(
 string $class,
 array  $parameters = []
);
\Fobber\Caller\Caller::callMethod(
 object $object,
 string $method,
 array  $parameters  = [],
 bool   $static_only = false
);
```

### 呼叫者基本用法

```
require_once __DIR__.'/path/to/autoload.php';

use \Fobber\Caller\{
 Validator,
 Caller
};

use \Fobber\Exceptions\{
 InvalidFunctionException,
 InvalidClassException,
 InvalidMethodException
};

$validator = new Validator;
$caller    = new Caller($validator);
```

#### 调用闭包

```
$value = $caller->
```

…</article>

[View on GitHub](https://github.com/fobber/caller)

嘿！我给作曲家出版了我的第一个图书馆。任何 PHP 开发人员都有时间阅读一些代码，你可以在 GitHub/Packagist 上查看我的包。

如果你读了代码，最好让我知道你的想法。

您可以通过 Composer 安装它，就像这样:

```
composer require fobber/caller 
```

这是一个调用函数、闭包、类和方法的库。你可能想知道为什么我会做这样的东西，但是我做了它，所以你可以利用它来调用动态值/处理程序。假设您正在构建一个路由器库。你希望能够把你的代码分成不同的文件，比如控制器。

所以你可以这么说:

```
$object = $caller->callClass(IndexController::class);
$method = '__invoke';
$parameters = [$request, $response];
$caller->callMethod($object, $method, $parameters); 
```

**或者在我实施后:**

```
$caller->call('IndexController', [$request, $response]); 
```

通过一个简洁的小功能，您可以拥有前缀和后缀参数。如果说你所有的控制器和它们的方法都需要一个`$request`和`$response`，你可以设置它。你要做的就是这个:

```
$caller->call('IndexController'); 
```