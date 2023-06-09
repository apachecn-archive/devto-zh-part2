# 雄辩的 UUIDs。

> 原文：<https://dev.to/bahdcoder/eloquent-uuids-eni>

在本教程中，我将向你展示如何以及为什么在你的雄辩模型中使用`uuid` s 作为主键。

## 什么是 UUIDs？

Uuid(唯一通用标识符)是一个 128 位的数字，用于识别互联网上的信息。根据所使用的具体机制，UUID 要么肯定不同，要么至少极有可能不同于公元 3400 年之前产生的任何其他 UUID(这意味着在接下来的一千多年里，无论使用何种机制，都不太可能再次产生相同的 UUID)。

因此，UUIDs 特别重要，因为它们是唯一的。

## 为什么要用 UUIDs？

*   UUIDs 不会泄露关于数据库记录的信息，所以在公共 URL 中使用它们更安全。比如在 URL 中看到`/users/12/`，强烈暗示存在 id 为 11 的用户，这就大大增加了被攻击的可能性。
*   它们在表、数据库和服务器中是唯一的，因此从一个数据库迁移到另一个数据库非常容易。
*   UUIDs 可以在任何地方生成，因此您可以知道下一个数据库记录的唯一标识符，而不必访问您的数据库。

## 将 UUIDs 集成到您的雄辩模型中。

我们将从安装一个新的 laravel 应用程序开始。

```
 laravel new eloquent-uuids 
```

Enter fullscreen mode Exit fullscreen mode

### 生成 UUIDs

我们要做的第一件事是建立一个生成 UUID 的方法。
第一个选择是使用一个为我们完成这项工作的外部包，我将向[推荐这个包](https://github.com/ramsey/uuid)。就我个人而言，我宁愿有一个自定义函数来做这件事，所以从 [PHP 文档](http://php.net/manual/en/function.uniqid.php#94959)中，我们将得到一个为我们生成 UUIDs 的函数。

```
 <?php

function uuid4()
{
    return sprintf(
        '%04x%04x-%04x-%04x-%04x-%04x%04x%04x',
        mt_rand(0, 0xffff),
        mt_rand(0, 0xffff),
        mt_rand(0, 0xffff),
        mt_rand(0, 0x0fff) | 0x4000,
        mt_rand(0, 0x3fff) | 0x8000,
        mt_rand(0, 0xffff),
        mt_rand(0, 0xffff),
        mt_rand(0, 0xffff)
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们把这个文件放在 laravel 应用程序的什么地方呢？我将在`/app/Helpers/uuid.php`中创建一个新文件，并将这个函数放入其中。

接下来，我将使用 composer 自动加载这个文件，这样这个函数将在我的 laravel 应用程序中全局可用。

```
//  composer.json  ...  "autoload":  {  "files":  [  "App/Helpers/uuid.php"  ]  }  ... 
```

Enter fullscreen mode Exit fullscreen mode

### 禁用 laravel 中的默认主键系统

要禁用主键的默认工作方式:

*   用字符串字段替换 id 的`increments`字段。

```
// database\migrations\create_users_table.php
Schema::create('users', function (Blueprint $table) {
  $table->string('id');
  $table->string('name');
  $table->string('email')->unique();
  $table->string('password');
  $table->rememberToken();
  $table->timestamps();
}); 
```

Enter fullscreen mode Exit fullscreen mode

*   将模型上的`incrementing`属性设置为`false`:

```
 // App\User.php

    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = false; 
```

Enter fullscreen mode Exit fullscreen mode

### 现出振振有词的表情`creating`勾

在这个模型上，我们将注册一个`creating`钩子，laravel 将在进行数据库查询以保存记录之前执行这个钩子。

```
// App\User.php

/**
 * Boot the Model.
 */
 public static function boot()
 {
    parent::boot();

    static::creating(function ($instance) {
       $instance->id = uuid4();
    });
 } 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！每当我们为`User`模型创建新的数据库记录时，就会生成一个 UUID，并自动保存到数据库中。

为了测试这一点，我添加了一个简单的路由，创建一个用户并返回新创建的用户。

```
 // routes/web.php

Route::get('/', function () {
    return \App\User::create([
        'name' => 'bahdcoder',
        'email' => 'bahdcoder@gmail.com',
        'password' => bcrypt('password'),
    ]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在浏览器中查看，我们会得到这个 json 响应:

```
 {  "name":  "bahdcoder",  "email":  "bahdcoder@gmail.com",  "id":  "c784692b-c7ea-4d76-9d5a-409df46d4cae",  "updated_at":  "2018-04-09 19:13:27",  "created_at":  "2018-04-09 19:13:27"  } 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们的 UUID 是在创建用户模型时自动生成的。

### 额外

对所有的模型都这样做将会有很多重复，所以我们可以有一个自定义的基础模型，它继承自 laravel 的基础模型，我们所有的模型都将从这个模型扩展而来。因此，让我们在我们的`app`目录:
中创建一个自定义的`Model.php`

```
 // app/Model.php

<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model as BaseModel;

class Model extends BaseModel
{
    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = false;

    /**
     * Boot the Model.
     */
    public static function boot()
    {
        parent::boot();

        static::creating(function ($instance) {
            $instance->id = uuid4();
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，对于任何进一步创建的模型，他们将扩展这个新的`app\Model.php`。

*   不要忘记将迁移中的`$table->increments('id');`更改为`$table->string('id');`。