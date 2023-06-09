# Laravel 中查询范围的魔力

> 原文：<https://dev.to/bertheyman/the-magic-of-query-scopes-in-laravel-pfp>

假设您正在建立一个网站，人们可以在那里为其他人输入书籍建议。作为预告，主页将包含 5 本书的建议。查询可以简单到:

```
// A simple select
Book::limit(5)->get(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，你可能选择了一些低质量的书籍建议，或者有知识的魂器书，最好永远隐藏起来。您决定，为了在主页上发布，书籍建议应该

*   设置为可见
*   创建于上个月
*   至少有 10 张赞成票

```
// Some quality control
Book::where('visible', 1)
    ->where('created_at', '>', Carbon::now()->subMonth())
    ->whereHas('votes', '>=', 10)
    ->limit(5)
    ->get(); 
```

Enter fullscreen mode Exit fullscreen mode

这给了你一些完美的书籍建议，所以你很满意，并留出一些时间来阅读它们。但是过了一段时间，你最终需要更多的检查来提高建议的质量。这可能会使查询有点混乱或难以阅读。

此时，您可能会考虑使用。通过将您的查询分解成构建块，它变得更容易阅读和重用。

# 具有查询范围的重构

看看下面这个来自 Laravel 的查询范围重构:

```
// App\Http\Controllers\BookController.php

// This syntax is very readable and easy to reuse
// Every building block is defined on your model
Book::popular()
    ->createdAfter(Carbon::now()->subMonth())
    ->limit(5)
    ->get();

// App\Book.php (model)

protected static function boot()
{
    parent::boot();

    // A global scope is applied to all queries on this model
    // -> No need to specify visibility restraints on every query
    static::addGlobalScope('visible', function (Builder $builder) {
        $builder->where('visible', 1);
    });
    // Bonus: if multiple models are hideable, this behaviour might
    // belong in a specific scope for easy reuse
}

// These are local scopes: ->popular() is added to the query to apply this where statement
public function scopePopular($query)
{
    // By defining the conditions to be a popular book,
    // it's easy to change them later on for all queries at once
    return $query->whereHas('votes', '>=', 10);
}

public function scopeCreatedAfter($query, $date)
{
    // A scope can be dynamic and accept parameters
    return $query->where('created_at', '>', $date)
} 
```

Enter fullscreen mode Exit fullscreen mode

使用作用域并不总是有用的，但是我发现当我的查询中需要更小的、可重用的块时，或者当与可读性作斗争时，它们特别有用。

如果您以前没有使用过查询范围，我希望您已经学到了一些东西。

你是如何处理(更复杂的)查询的？
随时留下评论诉说:

*   本文遗漏了什么对初学者有用的提示？
*   与示例相比，查询中有哪些不同之处？
*   在大型项目中用于组织查询的模式。存储库、查询范围、混合或完全不同的东西？

延伸阅读:[查询范围上的 Laravel 文档](https://laravel.com/docs/8.x/eloquent#query-scopes)(检查 Laravel 版本是否还是最新版本)
后续文章:[将模型功能分解到可重用组件中](https://dev.to/bertheyman/breaking-model-functionality-down-in-reusable-components-52nh)