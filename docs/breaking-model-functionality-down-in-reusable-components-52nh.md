# PHP 特性创建可重用组件的能力

> 原文：<https://dev.to/bertheyman/breaking-model-functionality-down-in-reusable-components-52nh>

最后，我写了关于使用 queryscopes 作为雄辩查询的构建模块的文章。让我们进一步探索图书网站，看看我们的模型中的可重用功能。

注意:有些功能是特定于 Laravel 的，但是核心思想可以用在大多数 PHP 框架中或者你认为合适的地方。

让我们看看我们把书模型放在哪里了。这个可见的范围将使我们能够很容易地隐藏所有的书籍建议，以防你 13 岁时的日记在某个地方结束。

```
// App\Book.php (model)

protected static function boot()
{
    parent::boot();

    // This code makes sure every query will automatically be restricted to visible models
    static::addGlobalScope('visible', function (Builder $builder) {
        return $builder->where('visible', 1);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，想象创建一个尚不可见的图书推荐:预览链接可能会方便地测试结果。

```
// App\Book.php (model)

public $previewKey = 'preview';

protected static function boot()
{
    parent::boot();

    // It might be wise to move the code to a dedicated scope class at this point
    // I've left it here for the sake of simplicity
    static::addGlobalScope('visible', function (Builder $builder) {
        if (!empty($model->previewKey) && request()->has($model->previewKey)) {
            return $builder;
        }

        return $builder->where('visible', 1);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

访问`/books/childhood-diary?preview=true`将绕过可见性约束并显示预览。我们对结果很满意，并继续增加功能:

*   一本书可以链接到标签
*   本书概述可以接受过滤器
*   ...

您现在想要创建作者模型，使用...标签，过滤器和隐藏性。复制-粘贴-完成？但是，我们希望通过我们的“懒惰的老开发者”,那么什么可能是最有效的长期选择呢？

```
// App\Book.php (model)

Use Filterable, Hideable, Taggable;

public $previewKey = 'preview';

// App\Traits\Hideable.php

namespace App\Traits;

trait Hideable
{
    protected static function bootHideable()
    {
        // This is the boot function of the Trait, code here will have the same                 behaviour as in the boot function of the model
        static::addGlobalScope('visible', function (Builder $builder) {
        if (!empty($model->previewKey) && request()->has($model->previewKey)) {
            return $builder;
        }

        return $builder->where('visible', 1);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用 Traits，很容易在不同的模型中重用、更新或删除行为——现在你已经准备好成为一名懒惰的开发人员了，祝贺你！

对此的任何反馈都非常欢迎！这包括:

*   你喜欢如何处理共享功能？如果你想告诉我不同的方法，请打电话给我。
*   关于这篇文章，有什么可以写得更好的吗？