# 在 Laravel 中轻松使用 UUIDs

> 原文：<https://dev.to/wilburpowery/easily-use-uuids-in-laravel-45be>

首次发布于我的[网站](https://wilburpowery.co/posts/easy-uuids-in-laravel/)

## 什么是 UUIDs？

UUID 代表**通用唯一标识符**。它是一个 128 位的数字，用来唯一地标识某个对象，或者在我们的例子中，标识数据库中的一条记录。

我不会深入讨论 UUIDs 与常规的自动递增 ID 相比的优缺点，那是一篇完整的博文。我要做的是向您展示，如果您愿意，在您的 Laravel 应用程序中使用 UUIDs 是多么容易。

## 准备您的迁移

在数据库中使用 UUIDs 的第一步是将列设置为特定的类型。默认情况下，Laravel 迁移在您创建的每个迁移中都包含一个`$table->primary('id');`列。

使用 UUIDs 就像更新我们的迁移以使用 Laravel 为我们提供的现成的`->uuid()`方法一样简单。例如，假设我们正在创建一个迁移来描述一篇文章的模式。

```
Schema::create('posts', function (Blueprint $table) {
    $table->uuid('id')->primary();
    $table->string('title');
    $table->text('body');
    $table->timestamps();
}); 
```

你应该注意的重要一行是这一行:`$table->uuid('id')->primary();`。注意我们使用的是`uuid()`方法，而不是常见的`increments()`方法。如您所见，`uuid()`方法指定该列应该是 UUID 等价列，我们还指定它应该是表上的主键。

## 创造 UUID

如果您运行`php artisan migrate`，并尝试使用工厂之类的东西创建一个新记录，或者手动创建，您将遇到一个错误，说明`id`列不能为空。

当在我们的迁移中使用`primary()`方法时，Laravel 会注意到它是一个自动递增的列，并为我们创建它。但是，由于我们切换到使用 UUIDs，我们将需要自己创建 ID。

### 使用雄辩模型事件

既然您使用的是 Laravel，我想您也会使用口才来轻松地与您的数据库交互。

雄辩有什么被称为模型事件。总之，它在不同的场景中总共触发了 11 个事件。您有以下事件:

*   `retrieved`
*   `creating`
*   `created`
*   `updating`
*   `updated`
*   `saving`
*   `saved`
*   `deleting`
*   `deleted`
*   `restoring`
*   `restored`。

如果你想了解更多关于雄辩事件的信息，你可以在这里找到它们的文档。

现在，回到创建我们的 UUIDs。让我们来看看你的`Post`模型可能是什么样子的:

```
class Post extends Model
{
    protected $guarded = []; // YOLO

    protected static function boot()
    {
        parent::boot();

        static::creating(function ($post) {
            $post->{$post->getKeyName()} = (string) Str::uuid();
        });
    }

    public function getIncrementing()
    {
        return false;
    }

    public function getKeyType()
    {
        return 'string';
    }
} 
```

我们的模型有 3 个方法。在`boot`方法中，我们可以连接到我们的模型并监听任何有说服力的事件。如果表上的 id 是递增的，到目前为止，concertive 一直使用`getIncrementing`方法。记住我们使用的是 UUIDs，所以我们将自动递增设置为`false`。
最后，`getKeyType`方法只是指定表上的 id 应该存储为字符串。

在我们的`boot`方法中，我们在听`creating`雄辩事件。这甚至会在记录实际存储到数据库之前触发。我们挂钩到这个事件，并使用 Laravel 中的`Str`类提供的`uuid()`方法。

不久前，人们可能已经用 Composer 安装了一个包来生成 UUID，但是您可以使用该类提供的`uuid()`方法轻松地生成它们。

很简单，您可以在 Laravel 中使用 UUIDs。

正如我最后提到的，我通常会有一个名为`UsesUuid`的 PHP 特性，其中我会有上面的逻辑。这样我就不会在我想使用 UUIDs 的每个模型上重复代码。

这就是`UsesUuid`特征的样子:

```
<?php

namespace App\Models\Concerns;

use Illuminate\Support\Str;

trait UsesUuid
{
    protected static function bootUsesUuid()
    {
        static::creating(function ($model) {
            if (! $model->getKey()) {
                $model->{$model->getKeyName()} = (string) Str::uuid();
            }
        });
    }

    public function getIncrementing()
    {
        return false;
    }

    public function getKeyType()
    {
        return 'string';
    }
} 
```

请注意，一切都更加一般化，而不是绑定到一个唯一的模型。

现在，在任何模型中，作为其迁移中的正确列，您可以简单地像这样使用`UsesUuid`特征:

```
class Post extends Model
{
  use App\Models\Concerns\UsesUuid;

  protected $guarded = []; // YOLO
} 
```

就是这样。只需几个简单的步骤，您就可以让 UUIDs 在您的 Laravel 应用程序中工作。