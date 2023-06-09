# 构建 PHP 框架:第 8 部分——路由

> 原文：<https://dev.to/mattsparks/building-a-php-framework-part-8---routing-4jgf>

***[原帖](http://developmentmatt.com/building-a-php-framework-part-7-the-container/)***

第 7 部分让我们了解了将在 Analyze PHP 框架中使用的容器。在本帖中，我们将介绍路由一般是如何工作的，特别是在 PHP 中。

## 路由的工作原理

路由是解析 URI 并决定采取适当措施的过程。

例如，考虑下面的 URI:

```
https://example.com/login 
```

Enter fullscreen mode Exit fullscreen mode

像上面这样的请求如何导致对用户的响应？让我们来分解一下:

1.  该请求由应用程序接收。

2.  应用程序将请求分解成多个组件。比如:方法(例如:GET)、主机、路径等等。

3.  应用程序寻找与该请求匹配的已定义路由。

4.  一旦找到，它就采取定义的动作并返回响应。

### 举个例子

对于一个真实的例子，这里有一种方法可以在 Laravel 中实现上面的例子。

```
Route::get('/login', function() {
    return view('login');
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们为/login URI 定义了一个 GET 路由。当请求该路由时，我们返回一个响应——在本例中，是登录页面的 HTML。

## 路由器如何工作

PHP 生态系统中有许多路由器。它们从更简单的到功能丰富的庞然大物都有。尽管它们在大小和复杂性上有所不同，但它们通常采用相同的基本步骤:解析请求、匹配模式、运行一些代码、返回响应。

Symfony 路由器(可能是使用最广泛的 PHP 路由器)就是这样做的。

这个也是。

还有[这个](https://github.com/nikic/FastRoute)。

## 一个非常简单的 PHP 路由器

为了演示这些概念，让我们创建一个愚蠢简单，一点用都没有的 PHP 路由器。

```
<?php
/**
 * First, let's define our Router object.
 */
class Router
{
    /**
     * The request we're working with.
     *
     * @var string
     */
    public $request;

    /**
     * The $routes array will contain our URI's and callbacks.
     * @var array
     */
    public $routes = [];

    /**
     * For this example, the constructor will be responsible
     * for parsing the request.
     *
     * @param array $request
     */
    public function __construct(array $request)
    {
        /**
         * This is a very (VERY) simple example of parsing
         * the request. We use the $_SERVER superglobal to
         * grab the URI.
         */
        $this->request = basename($request['REQUEST_URI']);
    }

    /**
     * Add a route and callback to our $routes array.
     *
     * @param string   $uri
     * @param Callable $fn
     */
    public function addRoute(string $uri, \Closure $fn) : void
    {
        $this->routes[$uri] = $fn;
    }

    /**
     * Determine is the requested route exists in our
     * routes array.
     *
     * @param  string  $uri
     * @return boolean
     */
    public function hasRoute(string $uri) : bool
    {
        return array_key_exists($uri, $this->routes);
    }

    /**
     * Run the router.
     *
     * @return mixed
     */
    public function run()
    {
        if($this->hasRoute($this->request)) {
            $this->routes[$this->request]->call($this);
        }
    }
}

/**
 * Create a new router instance.
 */
$router = new Router($_SERVER);

/**
 * Add a "hello" route that prints to the screen.
 */
$router->addRoute('hello', function() {
    echo 'Well, hello there!!';
});

/**
 * Run it!
 */
$router->run(); 
```

Enter fullscreen mode Exit fullscreen mode

### 运行代码

1.  将此代码在本地保存为 index.php。
2.  在您的终端中，导航到您保存脚本的目录。
3.  启动内置的 PHP web 服务器:`php -S localhost:1234`
4.  在浏览器中，转到:`http://localhost:1234/hello`

## 结论

我已经触及了路由的基本知识，分享了一些 PHP 世界中的路由示例，并构建了一个极其简单的路由器。另外，我已经开始研究将在 Analyze PHP 框架中使用的路由器。为了跟上进度，一定要在 Twitter 上关注 [@AnalyzePHP](https://twitter.com/AnalyzePHP) 。

最后一件事，请务必查看我的时事通讯！每周我都会给你发一封很棒的电子邮件，里面有更新、很棒的链接、提示&技巧和其他非开发人员的随机信息。如果你感兴趣，你可以通过点击这个链接[来注册。](http://eepurl.com/dvwlM5)