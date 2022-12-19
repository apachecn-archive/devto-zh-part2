# 如何在 Heroku 上部署一个 Laravel OAuth2 API(免费！)

> 原文：<https://dev.to/whoisryosuke/how-to-deploy-a-laravel-oauth2-api-on-heroku-for-free-4lpf>

您是否正在开发一个快速前端应用程序，并且需要一个**后端 API 来进行用户认证？**你可以从头开始组装一个 Node/Express API，并将其部署在 [Heroku](http://heroku.com) 上，但是除非你有一个很好的样板文件，否则你将花费*宝贵的时间*来集成依赖项，如 [PassportJS](http://www.passportjs.org/) 用于 [JWT 认证](https://jwt.io/introduction/)。也可能你不太懂服务器端的 JS，用 PHP 更舒服。这就是 Laravel 的用武之地。

**[Laravel](http://laravel.com)** 是一个 PHP 框架，它提供了一些基本的特性，比如 JWT 用户认证和开箱即用的路由*(带有官方插件)*。如果您知道一些正确配置的技巧和诀窍，在 Heroku 上部署会非常容易。

## 一个用于用户认证的(免费)API

今天，我们将创建一个基本的 Laravel 应用程序，带有用户注册/登录和应用程序的 JWT 认证 API。它将部署在 Heroku 的免费层，这是一个基于云的托管服务。

然后，您可以使用这个云托管的 API 将用户身份验证系统集成到 React、Vue、Angular 等开发的前端应用程序中。尽管你需要一个二级“网关”API 来连接这个 Laravel API，或者使用一个提供环境变量的服务(比如 [Netlify](http://netlify.com) ),因为前端应用程序会使 API 凭证变得脆弱。

*本文假设您已经在全球安装了 [Composer。你不必了解 Laravel 框架](https://getcomposer.org/download/)，但它会帮助你理解应用程序的结构。至少浏览一遍 Heroku 指南会有助于你在那里站稳脚跟。*

## 设置 Laravel

第一步是安装 Laravel。我们将使用 Composer 安装它，创建一个 SQLite 数据库，然后配置`.env`文件。

1.  使用 Composer 安装 Laravel:

```
composer create-project --prefer-dist laravel/laravel laravel-api-heroku 
```

Enter fullscreen mode Exit fullscreen mode

1.  打开项目文件夹(laravel-api-heroku)并转到`database`文件夹，创建一个名为`database.sqlite`的新文件。它应该是一个空文件——所以只需创建、保存并关闭它。

2.  回到项目的根目录，复制`.env.example`文件，并将新文件命名为`.env`。

3.  打开新的`.env`文件。将`DB_CONNECTION`变量改为等于`sqlite`，并删除该部分中的其他`DB_...`变量。该部分应该如下所示:

```
LOG_CHANNEL=stack

DB_CONNECTION=sqlite

BROADCAST_DRIVER=log
CACHE_DRIVER=file
SESSION_DRIVER=file
SESSION_LIFETIME=120
QUEUE_DRIVER=sync 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以开始引导 Laravel 的一些基本功能，如用户注册/认证。

## 设置认证 API

Laravel 使得创建一个用户可以创建帐户或登录的网站变得非常简单，但它将 Passport 包的简单性又向前推进了一步。我们不仅提供用户登录、注册、密码重置等现成功能，还提供 OAuth2.0 软件包和 Passport，允许您为自己的网站创建一种“社交登录”(当您访问私人用户数据时，请考虑 Twitter API)。

我们基本上会遵循 Laravel 文档中的相同说明，所以如果您迷路了，请查看这些说明。

运行
安装认证包

```
php artisan make:auth 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以安装 Passport 包了。

```
composer require laravel/passport 
```

Enter fullscreen mode Exit fullscreen mode

然后我们迁移新的数据库表，比如用户表或 API 键。

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用 Artisan:
安装 Passport

```
php artisan passport:install 
```

Enter fullscreen mode Exit fullscreen mode

我们向我们的用户模型添加了一个新的特征(`app/User.php` ):

```
<?php

namespace App;

use Laravel\Passport\HasApiTokens;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后你的`app/Providers/AuthServiceProvider.php` :
的`boot`方法内的`Passport::routes`方法

```
<?php

namespace App\Providers;

use Laravel\Passport\Passport;
use Illuminate\Support\Facades\Gate;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        'App\Model' => 'App\Policies\ModelPolicy',
    ];

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，在`config/auth.php`中，您应该将 api 认证守卫的驱动程序选项设置为 passport，以便用`TokenGuard` :
认证传入的请求

```
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    'api' => [
        'driver' => 'passport',
        'provider' => 'users',
    ],
], 
```

Enter fullscreen mode Exit fullscreen mode

> Protip:如果您以前安装了 Laravel，并且您正在更改配置，请确保通过在控制台中运行`php artisan config:cache`来清除配置缓存。

现在我们有了一个预构建的带有用户认证和 OAuth2.0 的 Laravel 应用程序。您可以通过运行`php artisan serve`并转到[本地开发服务器](http://localhost:8000)来测试用户认证。在那里你应该可以看到默认的 Laravel 登录页面，以及屏幕右上角的登录和注册按钮。

## 部署到 Heroku

如果你想为大多数类型的应用(NodeJS、PHP、Ruby 等)寻找快速和简单的部署，Heroku 是一个很棒的主机，有一个优秀的免费主机包。我们还将利用免费的 Postres 数据库托管层，为您提供 10k 数据库记录。

> 查看 Heroku 网站上关于部署 Laravel 的指南[。](https://devcenter.heroku.com/articles/getting-started-with-laravel)

首先，我们必须让 Heroku 服务器知道我们的应用程序实际上在`public/`文件夹中。让我们在项目根目录下创建一个名为`Procfile`的新文件，并添加以下内容:

```
web: vendor/bin/heroku-php-apache2 public/ 
```

Enter fullscreen mode Exit fullscreen mode

然后使用 CLI :
创建一个新的 Heroku 应用程序

```
heroku create 
```

Enter fullscreen mode Exit fullscreen mode

现在为生产站点添加您的应用程序密钥:

```
heroku config:set APP_KEY=$(php artisan --no-ansi key:generate --show) 
```

Enter fullscreen mode Exit fullscreen mode

提交任何更改并将主分支推送到 Heroku 远程回购:

```
git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以添加数据库了。通过进入[Heroku PostgreSQL](https://elements.heroku.com/addons/heroku-postgresql)页面并点击安装按钮，将 SQLite 数据库添加到您的 Heroku 应用程序中。然后从下拉列表中选择您的应用程序，并单击继续。你会被提示选择一个定价计划，我选择了免费的爱好计划。

现在，您可以通过更新您的`config/database.php`文件:
将您的应用程序[连接到 Heroku 的 Postgres 数据库](https://devcenter.heroku.com/articles/heroku-postgresql#connecting-in-php)

```
$DATABASE_URL = parse_url(getenv("DATABASE_URL"));
$DB_CONNECTION = parse_url(getenv("DB_CONNECTION"));

return [

    'default' => env('DB_CONNECTION', $DB_CONNECTION),
    // …

    'connections' => [

        // …

        'pgsql' => [
            'driver' => 'pgsql',
            'host' => $DATABASE_URL["host"],
            'port' => $DATABASE_URL["port"],
            'database' => ltrim($DATABASE_URL["path"], "/"),
            'username' => $DATABASE_URL["user"],
            'password' => $DATABASE_URL["pass"],
            'charset' => 'utf8',
            'prefix' => '',
            'schema' => 'public',
            'sslmode' => 'require',
        ],

        // …

    ],

    // …

]; 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要编辑环境变量，让 Laravel 知道在生产中使用 Heroku 时使用`pgsql`数据库驱动程序。在 Heroku 上找到你的项目，进入设置页面，点击显示配置变量。创建一个新条目，将键`DB_CONNECTION`和`pgsql`作为值。

现在，通过删除下面这一行:`/storage/*.key`，从`.gitignore`文件中删除本地生成的 OAuth 密钥。理想情况下，我们应该在生产中生成这些，但我在 Heroku 生成它们时遇到了问题，所以我们只导入本地的！

提交您的更改以使数据库连接生效:

```
git add .
git commit -m "Added Heroku database configuration"
git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

既然已经连接了数据库，请确保在生产环境中运行数据库迁移，以构建新的数据库。您将通过 Heroku CLI 运行它:

```
heroku run php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

你的 Laravel 网站应该是活的！您可以注册新用户，并使用您创建的凭据登录。

## 连接到您的新 API

如果您想通过某个用户向 API 发出一个经过身份验证的请求，您需要通过 OAuth2.0 验证过程，或者提供一个个人访问令牌(或 JWT)。Laravel Passport 允许您使用简单的命令行脚本创建新的客户端:

```
heroku run php artisan passport:client --personal 
```

Enter fullscreen mode Exit fullscreen mode

当提示输入应用程序名称时，将其命名为任意名称，然后单击 Enter。您将获得一个客户端 ID 和密码，用于远程访问 API。

如果你不熟悉 OAuth2.0 协议，这里有一个快速分解。OAuth2.0 通过在提供安全令牌来访问受中间件认证保护的 Laravel API 端点之前创建一个 3 步验证系统来保护您的 API。

1.  首先将用户重定向到 Laravel API 的 OAuth 授权页面(`http://yourlaravelapp.com/oauth/authorize`)，并将您的客户机 ID 和回调 URL 附加到请求中。

2.  用户要么看到一个登录屏幕——或者如果他们已经登录了——他们会看到一个授权表单，允许你的前端应用程序访问他们的数据(类似于当你使用社交登录时 Twitter 的授权)。然后，API 将用户重定向回您提供的回调 URL，并发送一个秘密令牌。

[![Laravel's default API permission authorization screen](../Images/d7b3fce44ff514720588d0b5b51e5185.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SJwBkAjy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://stayregular.net/content/2-blog/20180423-deploy-laravel-on-heroku-to-create-oauth2-0-api/laravel-passport-default-authorization-request.png)

1.  您获取这个秘密令牌，并将其与您的客户机 ID、秘密和回调 URL 一起发送回 API。API 用另一个秘密令牌进行响应，这一次是通过登录授权的特定用户的个人访问令牌。这个个人访问令牌是[a**JWT**T3】或者 **JSON Web Token** ，这是一种在 API 之间进行授权的行业标准方法。](https://jwt.io/)

每当我们想要访问 API 中需要身份验证的任何部分时，比如私有用户数据，我们就向 API 发送请求，请求头中带有授权承载令牌。我们在步骤 3 中生成的 JWT 中的令牌。

下面是 Laravel 中使用`session()`存储访问令牌的这种设置的一个片段。*请记住，这是一个单独的“网关”应用程序，它会使你的 API 证书对一个前端应用程序* :

```
 Route::get('/', function () {
    $query = http_build_query([
        'client_id' => '420',
        'redirect_uri' => 'http://127.0.0.1:8001/callback',
        'response_type' => 'code',
        'scope' => 'access-patient-status'
    ]);

    return redirect('http://127.0.0.1/oauth/authorize?'.$query);
});

Route::get('/callback', function (Request $request) {
    $response = (new GuzzleHttp\Client)->post('http://127.0.0.1/oauth/token', [
        'form_params' => [
            'grant_type' => 'authorization_code',
            'client_id' => '420',
            'client_secret' => 'EXKWFSwGHrbGdC3M4F5kkzmtCZIXyE3fbA8agtw2',
            'redirect_uri' => 'http://127.0.0.1:8001/callback',
            'code' => $request->code,
        ]
    ]);

    session()->put('token', json_decode((string) $response->getBody(), true));

    return redirect('/user');
});

Route::get('/user', function () {
    if ( ! session()->has('token')) {
        return redirect('/');
    }

    $response = (new GuzzleHttp\Client)->get('http://127.0.0.1/api/user', [
        'headers' => [
            'Authorization' => 'Bearer '.Session::get('token.access_token')
        ]
    ]);

    return json_decode((string) $response->getBody(), true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

您也可以在 React 应用程序中实现这一点，使用`react-router`创建回调路由，使用 Netlify 这样的服务安全地[访问您的构建环境中的 API 关键变量。](https://www.netlify.com/docs/continuous-deployment/#build-environment-variables)(而不是直接在你的产品代码中公开它们)。

## 让黑客马拉松开始吧

我希望这能激励你以新的方式处理你的项目。我们正处于从整体架构向基于微服务的架构的过渡时期，这种类型的设置将帮助您将项目迁移到这种新的基础架构中。JAMstack 革命正在进行，开发人员正在设计更多的前端应用程序，利用这些模块化的云分布式 API 解决方案。

你可以把你的应用程序的源代码放在一个 CDN 上，以实现最有效的分发。我们没有使用托管前端的同一台服务器来运行后端的登录或注册用户的请求，而是将该服务分解成一个独立的 API，驻留在它自己的服务器上。如果认证 API 服务器崩溃，它永远不会关闭网站 CDN(除了 API 提供的功能之外)。**这是未来！**

> 考虑利用 Laravel 的[社交包](https://laravel.com/docs/5.6/socialite)的力量将社交登录集成到你的 API 中，并允许用户使用 Twitter、脸书等社交媒体资料创建账户。

寻找源代码？💻⚙️在 Github 上找到这个完成的项目源代码[。](https://github.com/whoisryosuke/laravel-api-heroku)

保持规律，奥斯卡

* * *

**继续阅读:**

*   [Heroku -入门](https://devcenter.heroku.com/articles/getting-started-with-php#introduction)
*   Heroku -展开 Laravel
*   [Heroku-PostgreSQL 数据库托管](https://elements.heroku.com/addons/heroku-postgresql)
*   [Laravel 框架](https://laravel.com)
*   [Laravel -护照包](https://laravel.com/docs/5.6/passport)
*   [JSON 网络代币/ JWT](https://jwt.io/)
*   [Mikateach -在 Heroku 上设置 Laravel 5.6](https://mikateach.com/setting-up-laravel-5-6-on-heroku/)
*   [Pusher -使用 Laravel 构建 REST API](https://blog.pusher.com/build-rest-api-laravel-api-resources/)