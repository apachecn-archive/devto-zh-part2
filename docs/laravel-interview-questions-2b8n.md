# 拉勒韦尔面试问题

> 原文：<https://dev.to/sharadtricks/laravel-interview-questions-2b8n>

Laravel 是基于 MVC 设计模式的免费开源“PHP 框架”。它是由泰勒·奥特威尔创造的。Laravel 提供了富有表现力和优雅的语法，有助于轻松快速地创建精彩的 web 应用程序。
许多公司寻找了解 Laravel 框架的开发人员。在这篇文章中，你可以读到几个关于 Laravel 框架的最佳面试问题。

## 前 30 名 Laravel 面试问答。

以下是最新的 Laravel 面试问题和答案列表。

[1。什么是 Laravel？](https://www.onlineinterviewquestions.com/what-is-laravel/)

Laravel 是基于 MVC 设计模式的免费开源“PHP 框架”。它是由泰勒·奥特威尔创造的。Laravel 提供了富有表现力和优雅的语法，有助于轻松快速地创建精彩的 web 应用程序。

[2。使用 Laravel 框架有什么利弊？](https://www.onlineinterviewquestions.com/pros-cons-using-laravel-framework/)

##### 使用 Laravel 框架的优点

1.  Laravel framework 内置了轻量级刀片模板引擎，可以加快编译任务的速度，轻松创建包含动态内容的布局。
2.  困扰代码的可重用性。
3.  用 PHP 实现动态记录的雄辩的 ORM
4.  内置命令行工具" Artisan ",用于创建代码骨架、数据库结构和构建它们的迁移

##### 使用 laravel 框架的缺点

1.  开发过程要求你使用标准，并对编程有真正的理解
2.  Laravel 是新的框架，与 **npm(用于 node.js)** 、 **ruby gems** 和 **python pip** 相比，composer 并不强大。
3.  与 ruby on rails 相比，laravel 的开发速度没有那么快。
4.  Laravel 是轻量级的，所以与 django 和 rails 相比，它的内置支持更少。但是这个问题可以通过集成第三方工具来解决，但是对于大型和非常定制的网站来说，这可能是一个乏味的任务

[3。解释拉勒维尔的事件？](https://www.onlineinterviewquestions.com/explain-events-laravel/)

事件是由程序识别的可以由程序或代码处理的动作或事件。Laravel events 提供了一个简单的 observer 实现，允许您订阅和监听应用程序中发生的各种事件/操作。

所有事件类通常存储在 app/Events 目录中，而它们的侦听器存储在应用程序的 app/Listeners 中。

[4。解释 laravel 中的验证？](https://www.onlineinterviewquestions.com/explain-validations-laravel/)

在编程中，验证是一种简便的方法，可以确保数据在进入数据库之前始终是干净的、预期的格式。Laravel 提供了几种不同的方法来验证应用程序输入的数据。默认情况下，Laravel 的基本控制器类使用一个**validates requests****trait**来验证所有来自客户端的 HTTP 请求。您还可以通过创建表单请求来验证 laravel 中的数据。
[点击此处](https://laravel.com/docs/5.4/validation)阅读更多关于 Laravel 中数据验证的信息。

[5。如何通过 composer 安装 laravel？](https://www.onlineinterviewquestions.com/install-laravel-via-composer/)

您可以运行以下命令通过 composer 安装 Laravel。

```
 composer create-project laravel/laravel your-project-name version

Also Read **[Core PHP Interview Questions and Answers for 2018](https://www.onlineinterviewquestions.com/blog/core-php-interview-questions-2018/)**
```

[6。列出 laravel 5.0 的一些特性？](https://www.onlineinterviewquestions.com/list-features-laravel-5-0/)

*   内置 CRSF ( [跨站请求伪造](https://en.wikipedia.org/wiki/Cross-site_request_forgery))保护。
*   内置分页
*   [反向路由](https://www.laravelinterviewquestions.com/2017/03/what-is-reverse-routing-in-laravel.html)
*   查询生成器
*   路由缓存
*   数据库迁移
*   IOC(逆控制)容器或服务容器。

[7。什么是 PHP artisan。列出一些 artisan 命令？](https://www.onlineinterviewquestions.com/php-artisan-list-artisan-commands/)

**PHP artisan** is the command line interface/tool included with Laravel. It provides a number of helpful commands that can help you while you build your application easily. Here are the list of some artisan command:-

*   php 工匠列表
*   php 工匠帮助
*   php 工匠修补匠
*   php 工匠制作
*   PHP artisan-versian
*   php artisan 制作模型模型名称
*   php artisan make 控制器 controller_name

[8。列出一些 Laravel 5.4 提供的默认包？](https://www.onlineinterviewquestions.com/list-default-packages-provided-laravel-5-4/)

Below are list of some official/ default packages provided by Laravel 5.4

*   出纳员
*   使者
*   护照
*   侦察
*   社交名流

[9。Laravel 有哪些命名的路线？](https://www.onlineinterviewquestions.com/named-routes-laravel/)

**Named routing** is another amazing feature of Laravel framework. Named routes allow referring to routes when generating redirects or Url’s more comfortably. **You can specify named routes by chaining the name method onto the route definition:**

```
 Route::get('user/profile', function () {
    //
})->name('profile'); 
You can specify route names for controller actions:
 Route::get('user/profile', 'UserController@showProfile')->name('profile'); 

Once you have assigned a name to your routes, you may use the route's name when generating URLs or redirects via the global route function:
 // Generating URLs...
$url = route('profile');

// Generating Redirects...
return redirect()->route('profile'); 

```

10。什么是数据库迁移。如何通过 artisan 创建迁移？

**迁移**就像是对数据库的版本控制，允许您的团队轻松修改和共享应用程序的数据库模式。迁移通常与 Laravel 的 schema builder 配合使用，以轻松构建应用程序的数据库模式。

使用以下命令通过 artisan 创建迁移数据。

```
// creating Migration
php artisan make:migration create_users_table

```

[11。什么是服务提供商？](https://www.onlineinterviewquestions.com/what-are-service-providers/)

服务提供者是所有 laravel 应用程序引导的中心。您的应用程序以及所有 Laravel 核心服务也是由服务提供商引导的。
所有的服务提供者都扩展了 Illuminate \ Support \ service provider 类。大多数服务提供者包含一个注册和一个引导方法。在 register 方法中，您应该只将东西绑定到服务容器中。永远不要试图在 register 方法中注册任何事件侦听器、路由或任何其他功能。
您可以从[这里](https://laravel.com/docs/5.4/providers)了解更多关于服务提供商的信息

**阅读 2018 年最新 Codeigniter 面试问题。**

[12。解释一下 Laravel 的服务容器？](https://www.onlineinterviewquestions.com/explain-laravels-service-container/)

Laravel 最强大的特性之一是它的**服务容器**。它是在 Laravel 中解析类依赖和执行依赖注入的强大工具。
**依赖注入**是一个花哨的短语，本质上意味着类依赖通过构造函数或者在某些情况下通过“setter”方法被“注入”到类中。

13。什么是作曲家？

**Composer** 是 PHP 中管理依赖关系的工具。它允许您声明项目所依赖的库，并为您管理(安装/更新)它们。
**Laravel** 利用 Composer 管理其依赖关系。

[14。Laravel 中的依赖注入是什么？](https://www.onlineinterviewquestions.com/dependency-injection-laravel/)

在软件工程中，依赖注入是一种由一个对象提供另一个对象的依赖的技术。依赖是可以使用的对象(服务)。注入是将依赖关系传递给使用它的依赖对象(客户机)。服务成为客户端状态的一部分。[1]将服务传递给客户机，而不是让客户机构建或找到服务，是该模式的基本要求。
来源(【https://en.wikipedia.org/wiki/Dependency_injection】T2)。
你可以通过构造函数、设置器和属性注入来进行依赖注入。

15。什么是拉勒维尔合同？

Laravel 的契约只不过是定义 Laravel 框架提供的核心服务的一组接口。
[阅读更多关于拉勒维尔合同的](https://laravel.com/docs/5.4/contracts)

阅读最新 **[Symfony 面试问题](https://www.onlineinterviewquestions.com/symfony-interview-questions/)**

16。解释 Laravel 的立面？

Laravel Facades 为应用程序的服务容器中可用的类提供了一个静态的接口。Laravel 自带了许多 faces，这些 faces 提供了对 Laravel 的几乎所有特性的访问。Laravel facades 充当服务容器中底层类的“静态代理”,提供了简洁、有表现力的语法，同时保持了比传统静态类方法更好的可测试性和灵活性。Laravel 的所有外观都在 Illuminate\Support\Facades 名称空间中定义。您可以像这样轻松地访问外观:

```
 use Illuminate\Support\Facades\Cache;

Route::get('/cache', function () {
    return Cache::get('key');
}); 

```

[17。什么是拉勒维尔口才？](https://www.onlineinterviewquestions.com/what-are-laravel-eloquent/)

Laravel 的雄辩的 ORM 是一个简单的活动记录实现与您的数据库。Laravel 提供了许多不同的方式来与你的数据库交互，雄辩是其中最值得注意的。每个数据库表都有一个相应的“模型”，用于与该表进行交互。模型允许您查询表中的数据，以及向表中插入新记录。

下面是使用 concertive 在数据库中查询和插入新记录的示例。

```
 // Querying or finding records from products table where tag is 'new'
$products= Product::where('tag','new');
// Inserting new record 
 $product =new Product;
 $product->title="Iphone 7";
 $product->price="$700";
 $product->tag='iphone';
 $product->save(); 

```

18。如何在 Laravel 中启用查询日志？

使用 **enableQueryLog** 方法在 Laravel 中启用查询日志

```
 DB::connection()->enableQueryLog(); 
You can get array of the executed queries by using getQueryLog method:
$queries = DB::getQueryLog(); 

```

[19。什么是 Laravel 中的反向路由？](https://www.onlineinterviewquestions.com/reverse-routing-laravel/)

Laravel 反向路由基于路由声明生成 URL。反向路由使您的应用程序更加灵活。它定义了链路和 Laravel 路由之间的关系。当使用现有路由的名称创建链接时，Laravel 会自动创建适当的 Uri。这是一个反向路由的例子。

//路由声明
**Route::get('login '，' users @ log in ')；**

使用反向路由，我们可以创建一个到它的链接，并传入我们定义的任何参数。如果没有提供可选参数，则会从生成的链接中删除这些参数。

 **它会自动生成一个像[http://xyz.com/login](http://xyz.com/login)在视图中的网址。

**[阅读 40 个最佳 PHP 面试问题 2018](https://www.onlineinterviewquestions.com/core-php-interview-questions/)**

20。如何关闭 CRSF 保护特定路线在拉韦勒？

要关闭 Laravel 中的 CRSF 保护，请在“app/Http/Middleware/verifycsrftoken . PHP”中添加以下代码

```
 //add an array of Routes to skip CSRF check
private $exceptUrls = ['controller/route1', 'controller/route2'];
 //modify this function
public function handle($request, Closure $next) {
 //add this condition foreach($this->exceptUrls as $route) {
 if ($request->is($route)) {
  return $next($request);
 }
}
return parent::handle($request, $next);
} 
```

[21。拉勒维尔的特质是什么？](https://www.onlineinterviewquestions.com/what-are-traits-in-laravel/)

PHP Traits are simply a group of methods that you want include within another class. A Trait, like an abstract class cannot be instantiated by itself.Trait are created to reduce the limitations of single inheritance in PHP by enabling a developer to reuse sets of methods freely in several independent classes living in different class hierarchies. Here is an example of trait.

```
trait Sharable {

  public function share($item)
  {
    return 'share this item';
  }

}
**You could then include this Trait within other classes like this:**

class Post {

  use Sharable;

}

class Comment {

  use Sharable;

}
**Now if you were to create new objects out of these classes you would find that they both have the share() method available:**
$post = new Post;
echo $post->share(''); // 'share this item' 

$comment = new Comment;
echo $comment->share(''); // 'share this item'

```

[22。Laravel 支持缓存吗？](https://www.onlineinterviewquestions.com/laravel-support-caching/)

是的，Laravel 支持流行的缓存后端，如 **Memcached** 和 **Redis** 。
默认情况下，Laravel 被配置为使用文件缓存驱动程序，它将序列化的缓存对象存储在文件系统中。对于大型项目，建议使用 Memcached 或 Redis。

[23。解释一下 Laravel 的中间件？](https://www.onlineinterviewquestions.com/explain-laravels-middleware/)

顾名思义，中间件充当请求和响应之间的中间人。这是一种过滤机制。例如，Laravel 包括一个验证应用程序的用户是否经过身份验证的中间件。如果用户通过身份验证，他将被重定向到主页，否则，他将被重定向到登录页面。

Laravel 中有两种类型的中间件。
全局中间件:将在应用程序的每个 HTTP 请求上运行。
**路由中间件**:会被分配到特定的路由。
[阅读更多关于 Laravel 中间件](https://laravel.com/docs/5.4/middleware)

[24。卢蒙是什么？](https://www.onlineinterviewquestions.com/what-is-lumen/)

Lumen 是建立在 Laravel 顶级组件之上的 PHP 微框架。它是由泰勒·奥特威尔创造的。它是构建基于 Laravel 的微服务和快速 REST API 的完美选择。这是目前最快的微框架之一。
你可以通过运行以下命令使用 composer 安装 Lumen

```
composer create-project --prefer-dist laravel/lumen blog
```

[25。解释 Laravel 中的捆绑包？](https://www.onlineinterviewquestions.com/explain-bundles-laravel/)

在 Laravel 中，包也称为包。包是扩展 Laravel 功能的主要方式。包可以是任何东西，从处理诸如 Carbon 之类的日期的好方法，到 Behat 之类的完整 BDD 测试框架。在 Laravel 中，您也可以创建自己的定制包。你可以从[这里](https://laravel.com/docs/5.4/packages)阅读更多关于软件包的信息

[26。如何在 Laravel Modal 中使用自定义表格？](https://www.onlineinterviewquestions.com/use-custom-table-laravel-modal/)

您可以在 Laravel 中使用自定义表格，方法是覆盖 concertive 的 protected＄table 属性。

```
 Below is sample uses

class User extends Eloquent{
 protected $table="my_user_table";

} 

```

[27。列出 Laravel 口才中可用的关系类型？](https://www.onlineinterviewquestions.com/list-types-relationships-available-laravel-eloquent/)

以下是 Laravel 雄辩 ORM 支持的关系类型。

*   一对一
*   一对多
*   一对多(逆)
*   多对多
*   经历了许多
*   多态关系
*   多对多多态关系

你可以从[这里](https://laravel.com/docs/5.4/eloquent-relationships)阅读更多关于 Laravel 口才的关系

[28。为什么迁移是必要的？](https://www.onlineinterviewquestions.com/why-are-migrations-necessary/)

迁移是必要的，因为:

*   如果没有迁移，共享应用程序时的数据库一致性几乎是不可能的，尤其是当越来越多的人在 web 应用程序上协作时。
*   您的生产数据库也需要同步。

[29。提供安装 Laravel 5.4 的系统要求？](https://www.onlineinterviewquestions.com/provide-system-requirements-installation-laravel-5-4/)

为了安装 laravel，请确保您的服务器满足以下要求:

*   PHP >= 5.6.4
*   OpenSSL PHP 扩展
*   PDO PHP 扩展
*   Mbstring PHP 扩展
*   令牌化器 PHP 扩展
*   XML PHP 扩展

三十岁。列出 Laravel 中查询生成器提供的一些聚合方法？

*   计数()
*   最大()
*   最小值()
*   平均值()
*   总和()

还看了 [**Laravel 5 面试题 2018**](https://www.onlineinterviewquestions.com/laravel-interview-questions-and-answers/)**