# 使用内置服务器的 PHP 集成测试

> 原文：<https://dev.to/maxlmator/php-integration-tests-using-the-built-in-server-1k4e>

微服务已经随处可见，我已经在工作中写了一些。鉴于有限的范围，我发现与大多数(遗留的)整体相比，微服务是一种乐趣。在这篇文章中，我想分享如何使用内置服务器测试用 PHP 编写的微服务的方法，以及我们到目前为止遇到的一些陷阱。

让我们假设我们有一个很好的小型微服务，它是用 [Slim](https://www.slimframework.com/) 、 [Lumen](https://lumen.laravel.com/) 或者你喜欢的任何框架构建的。我们有一些返回 JSON 响应并接受各种 JSON 有效负载的路由。

我们能够使用 PHP 的内置服务器运行这个应用程序，例如运行:

```
ENVIRONMENT=production php -S 0.0.0.0:8080 -t public public/index.php 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`public/index.php`是任何 HTTP 请求的入口文件，在`http://localhost:8080`无法到达服务器。

另外，您已经有了一个覆盖单元测试的 [PHPUnit](https://phpunit.de/) 设置。为了简洁起见，我们将跳过整个 PHPUnit 设置部分，直接针对内置服务器编写测试。

* * *

## 基本设置

目标是编写一个测试用例，其中我们可能有一个主体有效负载，我们希望将它发送到特定的端点，然后检查响应是否具有 HTTP 状态`200`，返回的答案是否是包含特定信息的 JSON。像这样:

```
<?php
namespace Tests\Integration;

final class ControllerTest extends BaseTest
{
    public function testCorrectCall()
    {
        $body = json_encode(
          [
            'field_one' => 'some data',
            'field_two' => [
              'apple',
              'banana',
            ],
          ]
        );

        /** @var \Psr\Http\Message\ResponseInterface $response */
        $response = $this->dispatch($body, '/api/myfunction', 'POST');

        $this->assertSame($response->getStatusCode(), 200);
        $parsedResponse = json_decode($response->getBody(), true);
        $this->assertArraySubset(['message' => 'success'], $parsedResponse);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有趣的部分是解决`$this->dispatch`部分。我们提交端点、HTTP 方法和有效负载，并期望检索一个响应对象。所以`dispatch()`函数必须实际发送一个 HTTP 请求并返回响应对象。为了从实际测试中提取这一点，创建了一个`BaseTest`类:

```
<?php
namespace Tests\Integration;

use PHPUnit\Framework\TestCase;
use Symfony\Component\Process\Process;
use GuzzleHttp\Client;

abstract class BaseTest extends TestCase
{

    protected static $process;

    const ENVIRONMENT = "production";
    const HOST = "0.0.0.0";
    const PORT = 9876; // Adjust this to a port you're sure is free

    public static function setUpBeforeClass()
    {
        // The command to spin up the server
        $command = sprintf(
          'ENVIRONMENT=%s php -S %s:%d -t %s %s',
          self::ENVIRONMENT,
          self::HOST,
          self::PORT,
          realpath(__DIR__.'/../../public'),
          realpath(__DIR__.'/../../public/index.php')
        );
        // Using Symfony/Process to get a handler for starting a new process
        self::$process = new Process($command);
        // Disabling the output, otherwise the process might hang after too much output
        self::$process->disableOutput();
        // Actually execute the command and start the process
        self::$process->start();
        // Let's give the server some leeway to fully start
        self::usleep(100000);
    }

    public static function tearDownAfterClass()
    {
        self::$process->stop();
    }

    protected function dispatch($data = null, $path = null, $method = 'POST'): ResponseInterface
    {
        // STEP 2: Using Guzzle we send a request to the server
        $params = [];
        if ($data) {
            $params['body'] = $data;
        }

        // Creating a Guzzle Client with the base_uri, so we can use a relative
        // path for the requests.
        $client = new Client(['base_uri' => 'http://127.0.0.1:' . self::PORT]);
        return $client->request($method, $path, $params)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要做到这一点，您必须将这些包添加到您的开发依赖项中:

```
composer require --dev symfony/process
composer require --dev guzzlehttp/guzzle 
```

Enter fullscreen mode Exit fullscreen mode

### 这里发生了什么？

我们利用 PHPUnit 的`setUpBeforeClass()`和`tearDownAfterClass()`函数来控制本地 PHP 服务器的生命周期。在本地机器上执行 PHP 命令时， [symfony/process](https://github.com/symfony/process) 包给了我们一个很好的处理程序。我们不想为每个单独的测试重新创建服务器，每个`TestCase`级使用一个服务器就足够了。

在`dispatch()`函数中，我们使用 [guzzlehttp/guzzle](https://github.com/guzzle/guzzle) 创建一个请求。这被发送到服务器(使用与本地服务器相同的端口),它的`Response`对象被返回给调用者。

Et voilà the `testCorrectCall`是针对运行您的应用程序的本地 PHP 服务器执行的。

### 一些笔记

*   我们遇到了奇怪的问题，测试将永远被卡住，添加`self::$process->disableOutput();`为我们解决了这个问题。
*   如果你有多个继承自`BaseTest`的类，你可能想要使用不同的端口。我们遇到了系统尚未释放端口的问题。将`cont $port`换成`static $port`，或者增加或者减少它，可能会有所帮助。

* * *

## 结合远程测试和模拟环境

如上所述，大多数 PHP 开发框架都有在模拟环境中测试它们的方法——不需要启动本地 PHP 服务器。到目前为止，我们的测试还没有使用这种能力。

为了进一步增强测试套件，我们希望确定应用程序在直接访问和通过 HTTP 访问时的不同表现。事实上，我们使用远程测试的最初动机是这样一个场景，我们只在模拟环境中进行测试，测试中一切看起来都很好，却发现我们的服务在远程执行时(当然是在部署之后)会因为完全相同的调用而崩溃。

因此，我们目前使用的解决方案是对远程服务器和模拟环境进行测试:

```
<?php

namespace Tests\Integration;

use GuzzleHttp\Client;
use PHPUnit\Framework\TestCase;
use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\ResponseInterface;

abstract class BaseTest extends TestCase
{
    # Added to the part of BaseTest shown above

    protected function dispatch($data = null, $path = null, $method = 'POST', $headers = []): ResponseInterface
    {
        $localResponse = $this->dispatchLocal($data, $path, $method);
        $remoteResponse = $this->dispatchRemote($data, $path, $method);

        $this->ensureLocalAndHttpIdentical($localResponse, $remoteResponse);

        return $remoteResponse;
    }

    private function dispatchLocal($data = null, $path = null, $method = 'POST'): ResponseInterface
    {

        // TODO Mock your application as necessary, so it can be executed and returns an instance of the ResponseInterface
        $req = new Request($method, $uri, $headers, $cookies, $serverParams, $body);

        $this->app->getContainer()['request'] = $req;

        return $this->app->run(true);
    }

    private function dispatchRemote($data = null, $path = null, $method = 'POST'): ResponseInterface
    {
        $params = [];
        if ($data) {
            $params['body'] = $data;
        }

        $client = new Client(['base_uri' => 'http://127.0.0.1:' . self::PORT]);

        return $client->request($method, $path, $params);
    }

    protected function ensureLocalAndHttpIdentical(ResponseInterface $localResponse, ResponseInterface $remoteResponse)
    {
        $this->assertSame($localResponse->getStatusCode(), $remoteResponse->getStatusCode());
        $this->assertEquals((string)$localResponse->getBody(), (string)$remoteResponse->getBody());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，当我们现在在任何测试中使用`dispatch()`函数时，它将创建两个请求:一个针对被模仿的应用程序，一个针对本地 PHP 服务器的实际 HTTP 请求。因为我们不想单独测试两个请求的属性，所以我们比较主体和响应代码 int eh `ensureLocalAndHttpIdentical()`函数。这里的任何差异都会导致测试失败。然后`$remoteResponse`将被返回到实际测试中。

这并没有消除单元测试的必要性。但是在重构控制器或添加新功能时，它让我放心多了。对我来说，下一步将是把突变测试添加到我的测试方法中(最有可能是[感染](https://github.com/infection/infection))。