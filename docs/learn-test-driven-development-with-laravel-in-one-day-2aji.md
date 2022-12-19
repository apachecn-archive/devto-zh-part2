# 用 Laravel 一天学会测试驱动开发

> 原文：<https://dev.to/fitri/learn-test-driven-development-with-laravel-in-one-day-2aji>

我正在建立一个具有额外功能的网址缩写。最初，我开始同时构建前端和后端。但是由于范围对于一个附带项目来说变得太大了，我开始只构建后端以避免干扰流程。那是我开始使用 Postman 发送 GET/POST 请求的时候。

有一段时间，这很好，直到我厌倦了一遍又一遍地测试同样的东西。一定有更好的方法？幸运的是，有了 PHPUnit，这再简单不过了，PHPUnit 预装在 Laravel 中(不过您仍然需要在您的计算机上安装 PHPUnit)。

Laravel 文档涵盖了你需要知道的所有内容。要开始构建您的测试，您只需要运行:

`php artisan make:test ThingToTest`

这将在`tests/features`文件夹中创建一个名为 ThingsToTest 的文件。我的应用程序需要一些*种子*，我需要*在运行测试之前刷新数据库*，所以我把它们添加到我的测试文件:

```
use RefreshDatabase;

public function setUp() : void
{
    parent::setUp();
    $this->artisan('db:seed');
} 
```

Enter fullscreen mode Exit fullscreen mode

这基本上告诉我的测试在运行测试用例之前刷新迁移和种子数据库。我在这里使用默认的数据库，但是您可以为您的测试环境使用一个单独的数据库。

生成的测试文件中已经有了一个例子，所以您可以在命令行中键入`phpunit`来开始测试。我们没有添加任何自定义测试，所以应该可以通过。

现在我们可以开始制作我们自己的测试用例/方法了。下面是一个我如何测试我的 URL 是否被缩短的例子:

```
public function testCreateValidLink()
{
    // Perform Action
    $response = $this->json('POST', '/create/link', [
        'url' => 'https://dev.to/fitri',
        'name' => 'fitri'
    ]);

    // Test Result
    $response->assertJson(['created' => true]);
} 
```

Enter fullscreen mode Exit fullscreen mode

路由到我的链接控制器创建方法，该方法返回一个 JSON 对象。在我的例子中，该方法返回一个数组，其“创建”值设置为*真*或*假*。

我知道我可以使用 Faker 生成我的 *URL* 和*名称*。但是我的控制器验证了 URL，并确保它返回 200/301/302 http_code。所以在这种情况下我必须使用一个真实的 URL。

每个方法是测试类/文件代表一个测试。每个以“assert”为前缀的方法都是断言，当您运行`phpunit`时，这些断言要么返回通过，要么返回错误。断言方法有很多种。这些是我目前一直在用的:

*   $ this-> assert JSON([' key ' = > ' value '])；
*   $ this-> assertDatabaseHas(' table '，[' field ' = > ' value '])；
*   $ this-> assert redirect(' URL ')；

官方文件中列出了更多信息:

[https://laravel . com/docs/5.6/http-tests # available-assertions](https://laravel.com/docs/5.6/http-tests#available-assertions)
[https://laravel . com/docs/5.6/database-testing # available-assertions](https://laravel.com/docs/5.6/database-testing#available-assertions)

昨晚我刚刚学会了所有的东西，但这里有一些我从试验和错误中收集的有用的技巧:

*   测试文件必须以 Test (somethingTest.php)结尾。否则不会被 phpunit 运行。
*   测试用例(方法)必须以 test (testSomething())开始，否则将被跳过。
*   或者，您可以在您的方法前添加注释/** [@test](https://dev.to/test) */并将您的方法命名为任何名称。
*   测试中出现的错误是朋友。

当然，构建一些测试并不完全是 TDD。而是因为我依赖的是测试类而不是前端 app。我必须首先构建我的测试或者控制器/方法。无论哪种方式，它们都不是相互排斥的。

祝你好运！