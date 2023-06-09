# 创建活动票务服务(Laravel API+react js+NextJS+Material UI)

> 原文：<https://dev.to/whoisryosuke/creating-an-event-ticketing-service-laravel-api--reactjs--nextjs--material-ui-2c2b>

Laravel 使快速创建强大的后端应用程序和 API 变得非常容易。今天，我们将为一个活动目录和票务平台(如 Eventbrite)创建一个 API。任何前端或后端应用程序都可以使用这个 API，在本教程的下一部分，我们将创建一个基于 ReactJS 的管理仪表板，使用这个 API 来浏览、创建、编辑和删除帖子。

这个项目将基于一个品牌我的工作， [SeshSource](http://seshsource.com) ，这是一个专门为大麻为中心的活动票务平台。我们将在本质上创建 SeshSource 当前架构的镜像，只是在 Laravel 中，而不是 Wordpress + plugins。

## 必备功能(MVP)

我首先概述了我们希望 MVP 或最低可行产品具有的功能，比如浏览活动和购买门票:

### 目录

*   可按日期搜索的事件
*   按位置或类别显示的事件
*   活动组织者的用户配置文件
*   带有事件的日历视图
*   列表/卡片视图
*   所有城市，州等+档案页面列表

### 电子商务

*   购买免费或付费的票
*   生成票据的 PDF
*   通过电子邮件向用户发送 PDF
*   关于新采购的电子邮件业务
*   在活动中扫描门票的移动友好型网络应用程序

### 组织者工具

*   用于管理事件的仪表板
*   创建事件
*   编辑事件
*   删除事件
*   为特定活动创建门票类型(免费、VIP 等)
*   查看(+打印)活动参与者列表
*   查看(+打印)订单列表

现在我们知道了我们想要从 API 中得到什么，让我们开始编码吧！

## 用 Docker 设置一个 Laravel 项目

下面是我如何启动一个新的 Laravel 项目，并使用 Docker 进行本地开发的方法- *而不会把我的头发都拔掉*:

1.  `composer create-project --prefer-dist laravel/laravel seshsource-api`
2.  确保你把 Laradock *放在*你的项目文件夹里
3.  `cd laradock`
4.  将示例环境文件复制到真实环境文件:`cp env-example .env`
5.  将 Laravel root ENV 更改为使用 Laradock 作为主机( *Laravel 使用 localhost，但是 Laradock 使用服务名*访问 Docker 容器内的每个服务):

```
DB_HOST=mysql
DB_DATABASE=default
DB_USERNAME=default
REDIS_HOST=redis
QUEUE_HOST=beanstalkd 
```

Enter fullscreen mode Exit fullscreen mode

1.  `docker-compose up -d nginx mysql phpmyadmin redis workspace`

您应该在 Docker 开发服务器上本地部署您的 Laravel 项目！很简单，对吧？😁

## 4 步以内的 OAuth2.0 服务器

让我们为 Laravel 设置身份验证，并使用 Passport 包添加 OAuth2.0 支持:

1.  `composer require laravel/passport`项目根内
2.  `docker-compose exec workspace php artisan migrate`在 laradock 文件夹内(`cd laradock`)
3.  `docker-compose exec workspace php artisan passport:install`
4.  遵循 Laravel 文档中的指南向用户类添加特征
5.  `docker-compose exec workspace php artisan make:auth`
6.  这里是你设置应用程序名称的地方。它将在整个应用程序的“名称空间”中使用，因此引用模型将看起来像:`use SeshSource\Events`。

## uuid

我喜欢使用 UUIDs，而不是直接 id。如果您使用的是 Laravel 5.6 之前的版本，请安装这个库。否则，Laravel 5.6 及更高版本会预装一个`uuid()` [助手方法](https://laravel.com/docs/5.6/helpers#method-str-uuid)。你仍然需要创建一个特征，但是你不需要依赖。

1.  `composer require webpatser/laravel-uuid`
2.  将它添加到别名数组所在的 config/app.php 文件:`'Uuid' => Webpatser\Uuid\Uuid::class,`

现在我们只是在迁移中使用`$table->uuid('id'); $table->primary('id');`而不是`$table->increments('id');`。

确保从任何使用 UUIDs 的模型中删除自动增量:

```
/**
 * Indicates if the IDs are auto-incrementing.
 *
 * @var bool
 */
public $incrementing = false; 
```

Enter fullscreen mode Exit fullscreen mode

当我们需要创建新帖子时，创建一个新特征来处理 UUID 的生成(app/Traits/Uuid.php):

### 依赖版本

```
<?php

namespace SeshSource;

use Webpatser\Uuid\Uuid;
trait Uuids
{

    /**
     * Boot function from laravel.
     */
    protected static function boot()
    {
        parent::boot();

        static::creating(function ($model) {
            $model->{$model->getKeyName()} = Uuid::generate()->string;
        });
    }
}
?> 
```

Enter fullscreen mode Exit fullscreen mode

### 拉勒维尔 5.6 UUID

```
<?php

namespace SeshSource\Traits;

use Illuminate\Support\Str;

trait Uuids
{

    /**
     * Boot function from laravel.
     */
    protected static function boot()
    {
        parent::boot();

        static::creating(function ($model) {
            $model->{$model->getKeyName()} = (string) Str::uuid();
        });
    }
}
?> 
```

Enter fullscreen mode Exit fullscreen mode

在所有的 UUID 模型中加入特征:

```
use SeshSource\Traits\Uuids;

...

class User extends Authenticatable
{
use Uuids; 
```

Enter fullscreen mode Exit fullscreen mode

## 迁移和模型

Laravel 用一个简单的命令创建**模型**，帮助我们与数据交互，以及**迁移** - PHP 语句为我们创建数据库表(不需要 SQL！).我们将创建 10 个模型和数据库表来存储我们网站的所有数据。

关于结构的决策，我不会说太多细节，但它本质上是基于 Wordpress 的数据库结构——结合了 WooCommerce 和 Tickera 插件的数据库结构。事件存储在表中，任何额外的“元数据”存储在数据透视表中。这与购物车的“订单”类似。我们有一个专用于订单的表，以及一个用于每个单独行项目的数据透视表(包含价格和数量)。我们使用数据透视表，而不是将整个购物车存储在一个列中(用 JSON 序列化，用逗号分隔的 id，等等)——这允许我们利用 Laravel 的关系有效地加载任何相关数据。

在您的项目根目录中运行这些命令来创建模型和迁移:

```
php artisan make:model Events -m
php artisan make:model EventMeta -m
php artisan make:model TicketTypes -m
php artisan make:model TicketTemplates -m
php artisan make:model Tickets -m
php artisan make:model UserMeta -m
php artisan make:model Orders -m
php artisan make:model OrderItems -m
php artisan make:model CheckIns -m
php artisan make:model Reviews -m 
```

Enter fullscreen mode Exit fullscreen mode

将 UUIDs 添加到所有模型和迁移中。您可以选择不使用元表，但是出于安全目的，像订单和事件这样的东西应该有 UUIDs。

**示例型号:**

```
<?php

namespace SeshSource;

use SeshSource\Traits\Uuids;
use Illuminate\Database\Eloquent\Model;

class EventMeta extends Model
{
    use Uuids;

    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = false;
} 
```

Enter fullscreen mode Exit fullscreen mode

**示例迁移:**

```
class CreateEventMetaTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('event_meta', function (Blueprint $table) {
            $table->uuid('id'); 
            $table->primary('id');
            $table->timestamps();
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

重置数据库并迁移新结构:

`docker-compose exec workspace php artisan migrate:fresh`

> 如果您对“外来 id”有任何问题，这可能意味着在创建相关表之前已经运行了带有关系的迁移。重命名迁移，以确保在创建任何数据透视表之前创建任何相关的表。

## 认证使用用户模式

如果您注意到在迁移中我们向用户表(`database/migrations/2014_10_12_000000_create_users_table.php`)添加了一个“类型”。在这里，我们可以判断登录的用户是客户、活动组织者还是管理员。为了更容易地确定用户是否可以访问管理员或组织者级别的功能，我们在用户模型中添加了一个复选框:

```
 /**
     * Determines if user is admin or not (true or false)
     *
     * @return boolean
     */
    public function isAdmin() 
    {
        if($this->type == 'admin')
        {
            return true;
        } else {
            return false;
        }
    }

    /**
     * Determines if user is organizer or not (true or false)
     *
     * @return boolean
     */
    public function isOrganizer() 
    {
        if($this->type == 'organizer')
        {
            return true;
        } else {
            return false;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 创建 API 控制器

现在我们已经有了模型，我们可以处理应用程序的控制器部分了。现在我们需要 5 个控制器，我们将使用 Laravel 的 artisan CLI 来制作 [API 资源控制器](https://laravel.com/docs/5.6/controllers#resource-controllers)。这为我们启动了一个控制器，提供了标准 API 所需的所有必要方法(索引、存储、更新、删除)。

```
php artisan make:controller 'Api/EventsController' --api
php artisan make:controller 'Api/UsersController' --api
php artisan make:controller 'Api/ReviewsController' --api
php artisan make:controller 'Api/OrdersController' --api
php artisan make:controller 'Api/CheckInsController' --api 
```

Enter fullscreen mode Exit fullscreen mode

对于索引方法，我们只使用适当的模型和分页方法(`Events::paginate(10)`)。稍后我们将使用查询参数添加过滤(改变顺序、指定类别等)。

对于 show 方法，我们再次使用合适的模型，并使用`findOrFail()`方法来抓取`$id` : `$event = Events::findOrFail($id);`。

> 要让[使用 Laravel](https://laravel.com/docs/5.6/responses#json-responses) 返回一个 JSON 响应，只需对`response()`对象:`return response()->json($events);`使用`json()`方法

对于`store()`方法，我们为每个方法创建一个验证器(`php artisan make:request StoreEvents`)。验证器检查 POST 请求，并使用用户模型上的`isAdmin()`方法验证`authorize()`方法中的用户。

```
<?php

namespace SeshSource\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreEvents extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return $this->user()->isAdmin();
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'title' => 'required|unique:events|max:255',
            'start_date' => 'required|date',
            'end_date' => 'required|date',
            'street_address' => 'required',
            'city' => 'required',
            'state' => 'required',
            'email' => 'email',
            'event_logo' => 'image',
            'featured_img' => 'image'
        ];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## Passport 不支持 UUID 用户 ID

如果您在使用 Passport 为 OAuth2.0 认证应用程序创建客户端 id 时遇到任何问题，这里有一个快速解决方案。

因为我们把用户 ID 换成了 UUID，而不是递增的整数，所以 Passport 创建了一个名为`oauth_access_tokens`的表，它引用了递增的 ID，而不是 UUID。为了让它工作，我们必须创建一个新的迁移，删除该列并创建一个新列。

> 通常我们只是更新这个列，但是 Laravel 使用的`doctrine\dbal`库不支持 UUIDs。

1.  删除用户 id 列(因为 Laravel 的 dbal 依赖项不支持将列更改为 uuid) `php artisan make:migration drop_user_id_on_oauth_access_tokens`:

```
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class DropUserIdOnOauthAccessTokens extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('oauth_access_tokens', function (Blueprint $table) {
            $table->dropColumn('user_id');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('oauth_access_tokens', function (Blueprint $table) {
            $table->dropColumn('user_id');
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  添加迁移以将用户 ID 列插入为 UUID `php artisan make:migration update_oauth_access_tokens_with_uuids`:

```
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class UpdateOauthAccessTokensWithUuids extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('oauth_access_tokens', function (Blueprint $table) {
            $table->uuid('user_id')->change();
            $table->primary('user_id')->change();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('oauth_access_tokens', function (Blueprint $table) {
            $table->integer('user_id')->change();
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

重置数据库(再次)并迁移新结构:

`docker-compose exec workspace php artisan migrate:fresh`

## [启用 CORS](https://github.com/barryvdh/laravel-cors)

安装 CORS 依赖项并添加到 Kernel.php。这将允许您在同一服务器上创建一个应用程序，并启用与 API 的通信。当我们运行这个 API 时，它将被部署到`http://localhost/`。当我们为前端创建一个 React 应用程序(显示数据)时，它将部署到`http://localhost:3000`。因为它在同一个服务器上，但是不同的端口，你会收到一个跨域引用错误。

> 在 API 端点上启用 CORS 允许我们任何人访问任何服务器上的 API。因此，如果您打算部署到生产环境中，请确保将主机限制在您信任的服务器上(除非您的 API 是公共的)。

## 一天中的 API

就这样，您的 API 就可以供另一个应用程序使用了！

在本系列的下一部分中，我们将探索如何使用 NextJS 创建服务器端呈现的 ReactJS 管理仪表板，使用 Material UI 进行样式化，并使用我们的 OAuth2 API 进行身份验证。一句话里有很多单词和技术，文章本身也很难下咽，但我会尽可能让它简单。👍

敬请期待！🙈
良

* * *

**参考文献**:

*   [拉韦尔安装](https://laravel.com/docs/5.6/installation)
*   [http://laradock.io/documentation/](http://laradock.io/documentation/)
*   [https://medium . com/@ Steve azz/setting-up-uuids-in-laravel-5-552412 db 2088](https://medium.com/@steveazz/setting-up-uuids-in-laravel-5-552412db2088)
*   [https://github.com/webpatser/laravel-uuid](https://github.com/webpatser/laravel-uuid)
*   [命名您的 Laravel 应用程序](https://laravel.com/docs/5.0/configuration#after-installation)
*   [使用 Laravel Passport 快速创建 JWT 代币](https://stackoverflow.com/questions/41376928/laravel-5-3-passport-jwt-authentication)
*   [教义不支持将 MySQL 专栏改为 UUID](https://laracasts.com/discuss/channels/eloquent/uuid-type-change-to-nullable-through-migrations-not-working)
*   [启用 CORS](https://github.com/barryvdh/laravel-cors)
*   [使用受保护路由的 NextJS 登录示例](https://github.com/zeit/next.js/issues/153#issuecomment-257924301)