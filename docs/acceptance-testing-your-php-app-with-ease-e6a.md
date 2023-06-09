# 轻松验收测试你的 PHP 应用程序

> 原文：<https://dev.to/barryosull/acceptance-testing-your-php-app-with-ease-e6a>

验收测试是任何稳定系统的核心，它们是你确保它实际工作的方式，从开始到结束(我的偏好是首先编写它们，使用它们作为指南来确保我正在编写的特性按预期工作)。

写验收测试的时候，最好把系统当成一个[黑盒](http://softwaretestingfundamentals.com/acceptance-testing/)，输入进去，输出出来，就是这样。这证明了我们的应用程序的工作，可以与其他系统互动。一些框架内置了这一点，比如 [Laravel](https://laravel.com/) ，但并不是每个应用都是用这些框架编写的，事实上大多数都不是(尤其是遗留应用)，它们仍然需要测试。

如果你的应用是一个 HTTP 驱动的 API，你需要测试它是否接受 HTTP 请求。因此，您需要启动一个 web 服务器，配置它，向它发送 HTTP 请求，然后检查响应。您可能还会有一些控制台命令，所以您还需要为它们编写测试。让我们不要忘记检查数据库，这毕竟是一个输出。在 PHP 中，你是如何做到这一切的？

# PHP 内置 WebServer

首先，事实证明 web 服务器部分非常简单，PHP 自带了一个内置的(从 PHP 5.4 开始)。只需在应用程序的入口点运行`php -S 127.0.0.1:8000`，就万事大吉了。

当然，事情远不止如此。因为我们在测试中使用 PHPUnit(因为您为什么要使用其他东西呢？)，我们希望从验收测试中启动 web 服务器，然后向它发送请求。我们还希望测试完成后关闭 web 服务器。

为了让事情变得简单，我写了一个简单的类来处理上述问题。看看吧，下面我来解释一下细节。

```
<?php
declare(strict_types=1);

namespace Root\ProjectTests\Acceptance;

use GuzzleHttp\Client;

class WebApp
{
    private $host;
    private $entryPoint;

    private static $localWebServerId = null;

    public function __construct(string $host, string $entryPoint) 
    {
        $this->host = $host;
        $this->entryPoint = $entryPoint;
    }

    public function startWebServer()
    {
        if ($this->isRunning()) {
            return;
        }

        $this->launchWebServer();
        $this->waitUntilWebServerAcceptsRequests();
        $this->stopWebserverOnShutdown();
    }

    private function isRunning(): bool
    {
        return isset(self::$localWebServerId);
    }

    private function launchWebServer()
    {
        $command = sprintf(
            'php -S %s -t %s >/dev/null 2>&1 & echo $!',
            $this->host,
            __DIR__.'/../../'.$this->entryPoint
        );

        $output = array();
        exec($command, $output);
        self::$localWebServerId = (int) $output[0];
    }

    private function waitUntilWebServerAcceptsRequests()
    {
        exec('bash '.__DIR__.'/wait-for-it.sh '.$this->host);
    }

    private function stopWebServerOnShutdown()
    {
        register_shutdown_function(function () {
            exec('kill '.self::$localWebServerId);
        });
    }

    public function makeClient(): Client
    {
        return new Client([
            'base_uri' => 'http://'.$this->host,
            'http_errors' => false,
        ]);
    }
} 
```

这段代码启动一个 web 服务器，等待它开始接受请求，然后注册一个关闭函数，在所有测试完成后关闭 web 服务器。我们使用一个名为“等待”的脚本(这里有的[，它在继续之前等待 web 服务器上线。添加这一点是因为有时测试会在服务器实际活动之前开始。我们还确保多次调用`launchWebServer`不会导致任何问题。如果当前有一个 web 服务器正在运行，它就会停止。](https://github.com/vishnubob/wait-for-it)

一旦服务器开始运行，你就可以调用`makeClient`，这给了我们一个 http 客户端，特别是一个 Guzzle 客户端(同样，你为什么要使用其他东西)，它被配置为向服务器发送请求。现在您可以开始测试 HTTP 请求了。

# 配置

我们可以启动一个 web 服务器并向它发送请求，但是我们如何配置它呢？它使用什么数据库，在哪里记录错误？您最有可能使用环境变量来配置这些细节(和。env 文件来存储这些值)。一个解决方案是创建不同的。env 文件，然后在运行时加载正确的文件。这有点让人头疼，谢天谢地，这不是必需的。

PHPUnit 有一个用于环境变量的配置部分，这些变量是为每个 PHPUnit 进程自动加载的。这里有一个例子。

```
<php>
    <env name="APP_ENV" value="testing"/>
    <env name="DB_NAME" value="test_db"/>
    <env name="DB_HOST" value="127.0.0.1"/>
    <env name="DB_USER" value="root"/>
    <env name="DB_PASSWORD" value=""/>
    <!-- Ohh no, my precious DB credentials, please don't hack me! -->
</php> 
```

这是事情变得有趣的地方；由父测试流程创建的任何子流程也可以访问这些 env 变量。换句话说，我们通过测试中的`exec`启动的 web 服务器将自动预加载所有这些 env 变量，因此我们不必担心设置任何变量。它非常方便，让测试变得轻而易举。

# 持续整合

在这个阶段，我们有一个为本地测试配置的 web 服务器(通过 phpunit.xml)。这很好，但是当我们想要在 CI 服务器上运行这些验收测试时，比如 CircleCI 或 TravisCI，会发生什么呢？它们不会有与我们本地机器相同的配置细节，那么我们如何配置它们才能正常工作呢？

同样，事实证明这很简单。例如， [CircleCI](https://circleci.com/) 允许您在配置中定义环境变量，它会将这些变量预先加载到服务器中。现在，您可能认为这些变量会被 PHPUnit 覆盖，但是不用担心，phpunit.xml env 变量不会覆盖现有的 env 值。也就是说，如果您已经通过 CI 引导脚本为数据库设置了 env 变量，那么 PHPUnit 会让它们保持原样。这意味着上述 web 服务器将只在 CI 系统上工作，不需要修改代码。

# 控制台命令

当然，并不是所有的请求都是 HTTP，有时您会希望通过控制台运行命令。我们如何配置它？同样，这非常简单，我们在测试代码中通过`exec`运行命令。例如，这是一个 symfony 风格的命令调用。

```
exec("php bin/console app:create-user"); 
```

正如我之前说过的，任何由我们的测试启动的进程都将继承与父进程相同的 env 变量，所以我们的应用程序通过`exec`(或者`system`)运行所需的一切，不需要任何更改。

# 数据存储

对于验收测试来说，检查响应通常是不够的，您还需要检查是否将正确的数据发送到了数据库。我们如何从测试中访问数据库？

同样，这非常简单。PHPUnit 进程可以访问 env 变量，因此您只需创建一个 PDO 实例并直接对其运行查询。任何其他服务都是如此，无论是文件存储(s3)还是 API(尽管这比较棘手，我可能需要在某个阶段写一下)。更简单的是，使用您的应用程序容器(如 [PHP-DI](http://php-di.org/) )将这些服务注入到您已经预先配置好的测试代码中。任务完成。

# 就这么简单

因此，在本地对 PHP 应用程序进行验收测试是微不足道的。不仅如此，在 CI 系统上运行完全相同的流程也很容易，只需很少或不需要任何更改。在这一阶段，我希望你看到了它的吸引力，你可以很容易地把你的系统当成一个黑盒，这是一个获得代码稳定性和信心的好方法。