# 向 Laravel API 添加测试📝

> 原文：<https://dev.to/whoisryosuke/adding-testing-to-a-laravel-api--41n5>

最近我一直在尽我最大的努力进入 TDD，测试驱动开发，这是一种编程风格，你可以在你的代码旁边构建测试。不久前，我选择了 [Laravel](http://laravel.com) 作为许多项目的首选 PHP 开发框架，因为它让测试变得非常容易。Laravel 让它变得简单——从做测试(`php artisan make:test YourTest`)，到他们提供的用于与 PHPUnit 接口的易用 API(`$this->assertStatus(404)`)。

Laravel 在记录他们的框架方面做得很好，官方文档通常比我在网上找到的大多数代码库和教程更新。然而，测试文档也非常简单，缺少关键步骤(比如如何运行测试)。这让我想知道我是否错过了什么。

经过一点修修补补，这是我的发现。

## 测试 JSON APIs

Laravel 的最新版本有针对 JSON APIs 的特殊测试方法。它允许我们直接查询 Laravel 的内部 API，并轻松地对响应做出断言。

## 编写你的第一个测试

让我们先做测试。使用下面的`artisan`命令:

```
php artisan make:test ShopTest 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以打开位于`tests/Feature/ShopTest.php`中的第一个测试文件，并添加一个 JSON API 调用:

```
<?php

namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Foundation\Testing\WithFaker;
use Illuminate\Foundation\Testing\RefreshDatabase;

class ShopTest extends TestCase
{
    /**
     * Tests shop index without params to see if it works
     *
     * @return void
     */
    public function testShopIndex()
    {
        $response = $this->json('GET', '/api/v1/shops');
        $response
            ->assertStatus(201);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 工作原理

我们首先使用从 API 返回响应的`json()`方法查询 API。然后我们检查响应，**断言**某些东西，比如状态码(`assertStatus()`)或数据本身(`assertJson`)。

### 调试测试

如果在任何时候测试失败了，并且您不确定原因，只需执行`dd($response)`将响应转储到控制台。您还可以在测试期间使用类似于`var_dump($variable)`的命令在控制台中打印文本。它帮助验证一个方法何时返回一个布尔值(比如删除一个资源)或者验证一个资源上是否存在属性(比如`$post->id`)。对更大的变量(比如响应对象)使用`var_dump()`要小心——它会导致测试崩溃和冻结。

> 在断言具有重复属性名的数据时要小心，即使它们是嵌套的(例如`{ id: 1, comment { id: 129 } }`)。assertJson 将折叠数组，并假设您指的是两个 id。在这种情况下，您必须使用 assertExactJson 来检查重复的属性名。

## 运行测试

Laravel 实际上并没有在他们的文档中写这个，你可能会认为你可以用类似于`php artisan test`的东西来运行测试——但是你实际上必须在你的 composer 依赖项中使用 PHPUnit 库:

```
./vendor/bin/phpunit 
```

Enter fullscreen mode Exit fullscreen mode

从项目根目录运行该命令，PHPUnit 将运行位于您的`/tests/`文件夹中的所有测试。

> 请确保安装了所有的编写器依赖项，否则您将无法运行此脚本。如果您在 Docker 中运行 Laravel，请确保在容器`docker-compose exec workspace ./vendor/bin/phpunit`中运行 PHPUnit

### 运行单个测试

有时您需要单独运行一个测试。我也查了这个:

`./vendor/bin/phpunit --filter TestControllerName`

这也是可行的，尽管更加冗长:

`./vendor/bin/phpunit tests/Feature/TestControllerName.php`

## 保持简单的傻气

我发现对于基本的 Laravel APIs，您倾向于一遍又一遍地重复相同的测试。如果一个 API 测试看起来像我们的模型之一，就像编写一个带有受保护变量的扩展类一样简单，那不是很好吗？:

```
<?php
namespace Tests\Feature\Controllers;
use Tests\CrudTest;
use Illuminate\Foundation\Testing\WithFaker;
use Illuminate\Foundation\Testing\RefreshDatabase;
class StrainsControllerTest extends CrudTest
{
    /**
     * The model to use when creating dummy data
     *
     * @var class
     */
    protected $model = \KushyApi\Posts::class;
    /**
     * The endpoint to query in the API
     * e.g = /api/v1/<endpoint>
     *
     * @var string
     */
    protected $endpoint = 'strains';
    /**
     * Any additional "states" to add to factory
     *
     * @var string
     */
    protected $states = 'strains';
    /**
     * Extra data to pass to POST endpoint 
     * aka the (store() method)
     * 
     * Must be array (ends up merged with another)
     *
     * @var array
     */
    protected $store = [
        'category' => '1'
    ];
} 
```

Enter fullscreen mode Exit fullscreen mode

借助 OOP 中抽象类的力量，我们可以创建一种*基本*类来*扩展*我们特定端点的测试类的功能。让我们创建一个名为`tests/CrudTest.php` :
的新测试

```
<?php
namespace Tests;

use Tests\TestCase;
use Tests\Traits\AttachJwtToken;
use Illuminate\Foundation\Testing\WithFaker;
use Illuminate\Foundation\Testing\RefreshDatabase;

abstract class CrudTest extends TestCase
{
    use AttachJwtToken;

    /**
     * Uses the model factory to generate a fake entry
     *
     * @return class
     */
    public function createPost()
    {
        if($this->states)
        {
            return factory($this->model)->states($this->states)->create();
        }

        return factory($this->model)->create();
    }

    /**
     * GET /endpoint/
     * Should return 201 with data array
     *
     * @return void
     */
    public function testIndex()
    {
        $response = $this->json('GET', "api/v1/{$this->endpoint}");
        $response
            ->assertStatus(201)
            ->assertJson([
                'data' => true
            ]);
    }

    /**
     * GET /endpoint/<id>
     * Should return 201 with data array
     *
     * @return void
     */
    public function testShow()
    {
        // Create a test shop with filled out fields
        $activity = $this->createPost();
        // Check the API for the new entry
        $response = $this->json('GET', "api/v1/{$this->endpoint}/{$activity->id}");
        // Delete the test shop
        $activity->delete();
        $response
            ->assertStatus(201)
            ->assertJson([
                'data' => true
            ]);
    }

    /**
     * POST /endpoint/
     *
     * @return void
     */
    public function testStore()
    {
        $activity = $this->createPost();
        $activity = $activity->toArray();
        /**
         * Pass in any extra data
         */
        if($this->store)
        {
            $activity = array_merge($activity, $this->store);
        }
        $response = $this->json('POST', "api/v1/{$this->endpoint}/", $activity);
        ($this->model)::destroy($activity['id']);
        $response
            ->assertStatus(201)
            ->assertJson([
                'data' => true
            ]);
    }

    /**
     * DELETE /endpoint/<id>
     * Tests the destroy() method that deletes the shop
     *
     * @return void
     */
    public function testDestroy()
    {
        $activity = $this->createPost();
        $response = $this->json('DELETE', "api/v1/{$this->endpoint}/{$activity->id}");
        $response
            ->assertStatus(200);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该类测试 artisan 为您生成的基本 API 控制器中的索引、显示、存储和销毁方法。它使用您在端点的测试类中定义的变量动态地完成这项工作。

### 同工厂测试

这要求您的类有一个**工厂**，因此`createPost()`方法可以生成假数据进行测试。工厂使用 [Faker 库](https://github.com/fzaninotto/Faker)为特定模型生成虚假数据，无论你需要它来完成播种或测试任务。假设您需要创建 50 个用户——一个工厂将为所有 50 个用户生成随机的(甚至是唯一的)名称、密码和电子邮件。

创建工厂非常简单，[查看 Laravel 文档。](https://laravel.com/docs/5.6/database-testing#generating-factories)这是一个有几个不同类型字段(图像、位置数据等)的例子:

```
php artisan make:factory YourModelNameFactory 
```

Enter fullscreen mode Exit fullscreen mode

```
<?php

use Faker\Generator as Faker;

$factory->define(YourAppName\YourModelName::class, function (Faker $faker) {
    return [
        'id' => $faker->uuid,
        'name' => $faker->name,
        'slug' => $faker->slug,
        'featured_img' => $faker->imageUrl(640, 480),
        'avatar' => $faker->imageUrl(48, 48),
        'description' => $faker->paragraph,
        'latitude' => $faker->latitude(-90, 90),
        'longitude' => $faker->longitude(-180, 180),
        'address' => $faker->streetAddress,
        'city' => $faker->city,
        'state' => $faker->state,
        'postal_code' => $faker->postcode,
        'country' => $faker->country,
        'rating' => $faker->numberBetween(0, 5),
        'featured' => $faker->numberBetween(0, 1),
        'verified' => $faker->numberBetween(0, 1)
    ];
});

$factory->state(YourAppName\YourModelName::class, 'product', [
    'category' => 'Product',
]); 
```

Enter fullscreen mode Exit fullscreen mode

> 使用*状态*来添加额外的数据或修改您的假模型条目中的现有参数。如果您需要控制模型的特定参数，比如将用户设置为管理员或客户，那么这个功能非常有用。在这种情况下，我们设置文章的类别。

## 处理 JWT

如果你注意到了，我在抽象类上有一个特性叫做`AttachJwtToken`。我使用 Laravel 的 Passport 包，并使用 OAuth2 身份验证连接到 API。因此，测试中的所有 JSON 请求都需要正确的授权头(带有用户的 JWT)。

特性为我们处理这个过程。我最初在 Github 上由@jgrossi 找到了这个[，并以此为基础编写了我的代码。它检查现有的用户(可以使用`loginAs`方法传入)，如果没有找到用户，就创建一个用户，并返回该用户的 JWT(使用 Laravel Passport 的内置`createToken()`函数)](https://gist.github.com/jgrossi/4b1364e20418eca3ca937e70550c1823)

[查看我在 Gist 上的完整源代码](https://gist.github.com/whoisryosuke/2ee866ba6c32af5b4614ac6136a0012a)

## 就这么简单！

测试你的 API *(如果是一个简单的 CRUD API)真的很简单。*

研究测试最困难的部分是对我积累的信息量感到安全。最终，当您查看源代码时，Laravel/PHPUnit 提供的测试方法就只有这么多了。您断言这样或那样的东西，无论是状态代码还是 JSON 本身，或者使用`actingAs($user)`模仿用户。

变得**更加复杂的**是像复制跨越多个控制器、作业、服务等的功能这样的任务，或者模仿像图像存储这样的功能。或者只是为你所有的模型创建工厂。

测试很容易，设置适当的条件就成了真正的*测试*。

希望这对您的 API TDD 有所帮助🤘
良

* * *

**参考文献**:

*   [将 JWT 令牌附加到测试片段上](https://gist.github.com/whoisryosuke/2ee866ba6c32af5b4614ac6136a0012a)
*   [Laravel 文档-测试 JSON API](https://laravel.com/docs/5.6/http-tests#testing-json-apis)
*   [伪造文件](https://github.com/fzaninotto/Faker#fakerprovideruuid)
*   [测试驱动的 Laravel 课程(通过电子邮件支付免费样品)](https://course.testdrivenlaravel.com/)
*   [使用 PHPUnit 进行 Laravel 测试](https://dev.to/lechatthecat/how-to-use-phpunit-for-laravel-3d3c)
*   [Laravel 测试困境:令牌不匹配](https://dev.to/fatboyxpc/laravel-testing-woes-token-mismatch-28pd)