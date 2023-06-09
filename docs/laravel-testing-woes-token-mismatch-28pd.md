# Laravel 测试困境:令牌不匹配

> 原文：<https://dev.to/fatboyxpc/laravel-testing-woes-token-mismatch-28pd>

Laravel 内置了数量惊人的测试工具。任何了解我的人(或者读过我过去文章的人)都知道，我相信 Laravel 最好的部分之一是在测试集成方面投入了多少工作。不管你对 Laravel 有什么看法，看看他们的[测试文档](https://laravel.com/docs/5.6/testing)(确保查看其他链接的侧边导航)，它真的很棒。

测试的自然起点是 HTTP 测试，Laravel 称之为“特性测试”。对于外行来说，特性测试向您的应用程序发出一个模拟的 HTTP 请求，并允许您针对响应做出断言。

注:如果您已经熟悉这个问题，并且只对故障诊断的途径感兴趣，请随意跳过。

### 形势

时不时地，我发现自己在帮助一些似乎无法在基本 get 请求之外进行特性测试的人。虽然这很好，但是发出 POST/PUT/DELETE 请求可能也很重要，对吗？

为了澄清起见，让我们编写一个简单的 HTTP 测试来发出 POST 请求:

```
// routes/web.php:
Route::post('/', function () {
    return 'foo';
});

// tests/Feature/ExampleTest.php:
public function testFoo()
{
    $response = $this->post('/');
    $response->assertStatus(200);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您创建了一个全新的 Laravel 应用程序，这个测试绝对会通过；不幸的是，人们经常在应用程序开发周期的不同点开始编写测试，有时这个测试会失败。在这种特殊情况下，我要谈谈这个测试的具体故障:

```
1) Tests\Feature\ExampleTest::testFoo
Expected status code 200 but received 419.
Failed asserting that false is true. 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我承认这很神秘。HTTP 状态 419 是什么意思？甚至不在[维基百科文章](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#4xx_Client_errors)里！幸运的是，Laravel 5.5 在默认的`TestCase`对象上引入了一个非常酷的实用方法:`withoutExceptionHandling()`。我们所要做的就是在我们的`testFoo()`方法的顶部添加`$this->withoutExceptionHandling()`，任何被 Laravel 的[异常处理程序](https://laravel.com/docs/5.6/errors#the-exception-handler)处理的异常都将被重新抛出，我们将在控制台中看到它。如果你喜欢 Laravel 5.4 和更低版本中的这种行为，你可以看看 [Adam Wathan 的博客文章](https://adamwathan.me/2016/01/21/disabling-exception-handling-in-acceptance-tests/)，它启发了这个特性。

加上`$this->withoutExceptionHandling()`后，我们现在看到这个:

```
1) Tests\Feature\ExampleTest::testFoo
Illuminate\Session\TokenMismatchException:

(omitting the long stack trace) 
```

Enter fullscreen mode Exit fullscreen mode

有时，人们会在请求数据中添加一个令牌键和值来避免这个错误。当然，这是一个短期的解决方案，但是谁愿意为每个请求都写这个呢？幸运的是，这很容易排除故障！

### 故障排除

所有的例子都在 Laravel 5.6 中，因为在我写这篇文章的时候，这是当前的版本。

如果我们看一看 [HTTP 内核](https://github.com/laravel/laravel/blob/fa81e36841ee25c3440fc430ed8d6b66c641062b/app/Http/Kernel.php#L30-L38)的中间件 web 组，我们会看到`\App\Http\Middleware\VerifyCsrfToken::class`，这可能是一个很好的起点！大多数项目不会修改这个类，所以它看起来会是这样:

```
<?php

namespace App\Http\Middleware;

use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

class VerifyCsrfToken extends Middleware
{
    /**
     * The URIs that should be excluded from CSRF verification.
     *
     * @var array
     */
    protected $except = [
        //
    ];
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您从 Laravel 的早期版本升级，可能会略有不同，但概念将保持不变。注意这个类大部分是空的。我们需要遵从[父类](https://github.com/laravel/framework/blob/21709c981ee3e2844738bd57155d883ee47b7f06/src/Illuminate/Foundation/Http/Middleware/VerifyCsrfToken.php)来查看行为。因为这个类有 167 行，所以我只展示相关的部分:

```
 /**
     * Handle an incoming request.
     *
     * @param \Illuminate\Http\Request $request
     * @param \Closure $next
     * @return mixed
     *
     * @throws \Illuminate\Session\TokenMismatchException
     */
    public function handle($request, Closure $next)
    {
        if (
            $this->isReading($request) ||
            $this->runningUnitTests() ||
            $this->inExceptArray($request) ||
            $this->tokensMatch($request)
        ) {
            return $this->addCookieToResponse($request, $next($request));
        }
        throw new TokenMismatchException;
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果你不熟悉[的 Laravel 中间件](https://laravel.com/docs/5.6/middleware)，那就去读一读吧，因为它并不太难理解。现在，只需要知道所有的中间件都会调用`handle()`方法。老实说，走到这一步可能是最可怕的部分。这里很明显`$this->runningUnitTests()`一定是失败了。但是为什么呢？！我们绝对是在内部测试中运行的！

好吧，我们去兔子洞吧！我们来看看`runningUnitTests()`长什么样:

```
 protected function runningUnitTests()
    {
        return $this->app->runningInConsole() && $this->app->runningUnitTests();
    } 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这很简单，对吧？从这里开始，只需启动 Xdebug 并添加一个断点或放入一个`dd()`来检查`$this->app->runningInConsole()`和`$this->app->runningUnitTests()`的输出。所以[另一步](https://github.com/laravel/framework/blob/21709c981ee3e2844738bd57155d883ee47b7f06/src/Illuminate/Foundation/Application.php)下兔子洞我们走:

```
 /**
     * Determine if the application is running in the console.
     *
     * @return bool
     */
    public function runningInConsole()
    {
        return php_sapi_name() === 'cli' || php_sapi_name() === 'phpdbg';
    }

    /**
     * Determine if the application is running unit tests.
     *
     * [@return](http://twitter.com/return) bool
     */
    public function runningUnitTests()
    {
        return $this['env'] === 'testing';
    } 
```

Enter fullscreen mode Exit fullscreen mode

虽然很有可能`runningInConsole()`会返回 true，但验证一下也无妨。出于本文的目的，我将假设它确实返回 true，并且`runningUnitTests()`失败。现在，显而易见的地方是`config/app.php`，那里有一条专门针对`'env' => env('APP_ENV', 'production')`的线路。很有可能你的`.env`档案里有`APP_ENV=local`。这就是问题所在，它说的是本地而不是测试！嗯，没那么快。Laravel 的默认安装在`phpunit.xml` : `<env name="APP_ENV" value="testing"/>`中有这一行。当然有必要验证 line 是否存在，值是否仍然设置为 testing。

### 可能的疑点

如果没那么容易呢？`phpunit.xml`中的一切看起来都很好，出于极度的偏执我们把`.env`改成了`APP_ENV=testing`(只是澄清一下，你实际上不需要改变那个，可能也不应该)，但我们还是看到`$this['env']`不是`testing`。在这一点上，您将想要开始查看您所在的测试类和`tests/TestCase.php`中的`setUp()`方法。

您需要注意以下任何一种情况:

*   `config(['app.env' => 'not-testing'])`
*   `config()->set('app.env', 'not-testing')`
*   `Config::set('app.env', 'not-testing')`
*   `putenv('APP_ENV=not-testing')`
*   `App::detectEnvironment()`
*   `App::detectEnvironment()`

注意:`detectEnvironment()`将有一个回调函数传递给它。除非您的项目从早期版本升级到 Laravel 5，否则您可能不必担心这个问题。

当您浏览`setUp()`方法时，您可能希望确保在使用它时总是调用`parent::setUp()`,以防止其他负面影响。同样值得一提的是`createApplication()`函数(在 Laravel 5.4 及更高版本中，它被移到了一个特征中)。

如果您仍然空手而归，可能值得在整个`tests`目录中搜索您在`$this['env']`中看到的值，和/或检查您可能拥有的任何种类的 vagger/Homestad/Docker 配置文件。

虽然某个地方的测试文件中的某些更改可能是罪魁祸首，但有人可能特别邪恶，在中间件之前更改应用程序/请求生命周期中的环境，这是不值得的。如果有人想在`bootstrap/app.php`或者甚至是在`VerifyCsrfToken`之前加载的中间件中获得乐趣。前面讨论的 HTTP 内核有一个可以被覆盖的[中间件优先级](https://github.com/laravel/framework/blob/8af242bd8cc683acfc8ea43be076d8f5b9249606/src/Illuminate/Foundation/Http/Kernel.php#L73)实例变量。

这篇文章最终比我最初预期的要长一点，但是希望这给你更多的信心去自己浏览 Laravel 的源代码，它真的没有那么可怕！