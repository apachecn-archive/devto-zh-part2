# Nova 应用程序的标签栏

> 原文：<https://dev.to/freekmurze/a-tags-field-for-nova-apps-1fg6>

不久前，我放弃了这个博客的自定义管理员，转而选择了 T2·拉勒维尔·诺瓦。你可能已经注意到 murze.be 上的每篇博文都有一些标签。Nova 配备了[许多表单域](https://nova.laravel.com/docs/1.0/resources/fields.html#fields)，但是一个使用标签的域并不是开箱即用的。这就是为什么我创建了自己的标签字段，并将其发布为 [spatie/nova-tags-field](https://github.com/spatie/nova-tags-field) 。在这篇文章中，我想向你介绍它。

## 使用字段

使用标签字段非常简单。你只需要把它添加到你的 Nova 资源中。

```
namespace App\Nova;

// ...

class BlogPost extends Resource
{
    // ...

    public function fields(Request $request)
    {
        return [
            // ...

            Tags::make('Tags'),

            // ...
        ];
    }
} 
```

这就是你需要做的。这是该字段的外观:

[![Screenshot of the murze.be admin](img/e0cd0e5317e3979cac73b1951c079cd8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3HSKoZmY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/nova-tags-field/screenshot.png)

键入标签时，该字段将自动建议现有的标签。如果你不想要自动暗示，你可以这样关掉它:

```
return [
   Tags::make('Tags')->withoutSuggestions(),
]; 
```

## 使用类型

该字段还支持多种标记类型。如果你想在你的资源上使用多个标签域，你可以这样做:

```
return [
   Tags::make('Tags')->type('author'),
   Tags::make('Tags')->type('subject'),
]; 
```

第一个标签字段现在将只建议现有的作者，第二个将建议现有的主题。

## 只允许一个标签

如果您希望只选择一个标签，可以使用 single 方法。

```
return [
   Tags::make('Tags')->single(),
]; 
```

该字段将呈现为选择的表单元素。它将由已经保存的标签的名称填充。

## 管理 Nova 中的标签

如果您想对保存标记执行 crud 操作，只需为它创建一个 Nova 资源。这里有一个例子。

```
namespace App\Nova;

use Illuminate\Http\Request;
use Laravel\Nova\Fields\Text;
use Spatie\Tags\Tag as TagModel;

class Tag extends Resource
{
    public static $model = TagModel::class;

    public static $title = 'name';

    public static $search = [
        'name',
    ];

    public function fields(Request $request)
    {
        return [
            Text::make('Name')->sortable(),
        ];
    }
} 
```

## 以编程方式处理标签

在引擎盖下，nova field 使用[我们的 laravel-tags 包](https://docs.spatie.be/laravel-tags/v2/introduction)，这使得使用标签变得非常容易。这里有几个例子:

```
// create a model with some tags
$post = Post::create([
   'name' => 'testModel',
   'tags' => ['tag', 'tag2'], //tags will be created if they don't exist
]);

$post->tags; // returns a collection with all attached tags

// attaching tags
$post->attachTag('tag3');
$post->attachTags(['tag4', 'tag5']);

// detaching tags
$post->detachTag('tag3');
$post->detachTags(['tag4', 'tag5']);

// syncing tags
$post->syncTags(['tag1', 'tag2']); // all other tags on this model will be detached

// retrieving models that have any of the given tags
Post::withAnyTags(['tag1', 'tag2']);

// retrieve models that have all of the given tags
Post::withAllTags(['tag1', 'tag2']); 
```

我们的 nova-tags-field 包也将调用这些方法。

## 关闭思绪

你会在 GitHub 上找到 nova-tags-field [的安装说明。如果您喜欢这个 nova 域，请务必查看我们的其他两个 Nova 包:](https://github.com/spatie/nova-tags-field)

*   [新星-尾巴-工具](https://github.com/spatie/laravel-tail-tool)
*   [新星-备份-工具](https://github.com/spatie/laravel-backup-tool)

即使你对 Laravel Nova 不感兴趣，你也可能会在我的团队之前制作的这个大的包列表中为你的下一个项目找到一些东西。