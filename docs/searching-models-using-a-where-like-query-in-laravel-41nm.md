# ★在 Laravel 中使用 where like 查询搜索模型

> 原文：<https://dev.to/freekmurze/searching-models-using-a-where-like-query-in-laravel-41nm>

对于我正在做的一个项目，我需要建立一个轻量级的、实用的搜索。在这篇博文中，我想回顾一下我的解决方案。

## 寻找雄辩的模特

假设您需要为用户提供一个搜索。使用口才，你可以执行这样的搜索:

```
User::query()
   ->where('name', 'LIKE', "%{$searchTerm}%") 
   ->orWhere('email', 'LIKE', "%{$searchTerm}%") 
   ->get(); 
```

这将返回名称或电子邮件包含`$searchTerm`中的字符串的所有记录。如果你使用 MySQL，这个搜索也将以不区分大小写的方式执行，这可能是你想要的。

## 使用宏

现在，如果您想添加一个搜索，不仅是对`User`模型，而是对每个模型，您可以添加一个宏到雄辩的查询生成器。如果你从来没有听说过 Laravel 中的宏，可以看看紧缩公司博客上的这篇精彩博文。

下面是我们的宏可能的样子。你可以把它称为`App\Providers\AppServiceProvider`的`boot`方法，或者是你自己的服务提供商。

```
use Illuminate\Database\Eloquent\Builder;

// ...

Builder::macro('whereLike', function(string $attribute, string $searchTerm) {
   return $this->orWhere($attribute, 'LIKE', "%{$searchTerm}%");
}); 
```

我们现在可以这样搜索我们的模型:

```
User::query()
   ->whereLike('name', $searchTerm)
   ->whereLike('email', $searchTerm)
   ->get(); 
```

## 改善我们的宏观

还有改进的空间。我不喜欢我们现在必须为我们想要搜索的每个属性重复这个`whereLike`调用。让我们解决这个问题。这是我们宏的改进版本。

```
Builder::macro('whereLike', function($attributes, string $searchTerm) {
   foreach(array_wrap($attributes) as $attribute) {
      $this->orWhere($attribute, 'LIKE', "%{$searchTerm}%");
   }

   return $this;
}); 
```

是一个不错的小助手。当给定一个数组时，它只返回这个数组。当给定其他东西时，它将把它包装在一个数组中。所以你知道结果总是一个数组。

上面的宏可以这样用:

```
// searching a single column
User::whereLike('name', $searchTerm)->get();

// searching multiple columns in one go
User::whereLike(['name', 'email'], $searchTerm)->get(); 
```

## 修复宏

我们的宏看起来已经很不错了，但是它有一个严重的错误。

考虑这个查询:

```
User::query()
   ->where('role', 'admin')
   ->whereLike(['name', 'email'], 'john')
   ->get(); 
```

如果您认为这将只返回具有`admin`角色的用户，那么您就错了。因为我们的`whereLike`宏包含`orWhere`，这将返回每个角色为`admin`的用户，以及姓名或电子邮件包含`john`的所有用户。

让我们通过将我们的`orWhere`封装在一个函数中来解决这个问题。这相当于在搜索查询中设置括号。

```
Builder::macro('whereLike', function ($attributes, string $searchTerm) {
    $this->where(function (Builder $query) use ($attributes, $searchTerm) {
        foreach (array_wrap($attributes) as $attribute) {
            $query->orWhere($attribute, 'LIKE', "%{$searchTerm}%");
        }
    });

    return $this;
}); 
```

现在，上面的查询将返回名称或电子邮件包含`john`的所有管理员。

## 添加对关系的支持

现在，我们只能在使用作用域的地方搜索模型的属性。让我们添加对搜索该模型的关系属性的支持。

```
Builder::macro('whereLike', function ($attributes, string $searchTerm) {
    $this->where(function (Builder $query) use ($attributes, $searchTerm) {
        foreach (array_wrap($attributes) as $attribute) {
            $query->when(
                str_contains($attribute, '.'),
                function (Builder $query) use ($attribute, $searchTerm) {
                    [$relationName, $relationAttribute] = explode('.', $attribute);

                    $query->orWhereHas($relationName, function (Builder $query) use ($relationAttribute, $searchTerm) {
                        $query->where($relationAttribute, 'LIKE', "%{$searchTerm}%");
                    });
                },
                function (Builder $query) use ($attribute, $searchTerm) {
                    $query->orWhere($attribute, 'LIKE', "%{$searchTerm}%");
                }
            );
        }
    });

    return $this;
}); 
```

用那个宏可以做这样的事情:

```
Post::whereLike(['name', 'text', 'author.name', 'tags.name'], $searchTerm)->get(); 
```

## 在关闭

上面的宏完全满足了我在项目中的需求。但是你可以更进一步。这是塞尔吉奥·布鲁德做的一个变体，将搜索词分开。

> 基于 [@freekmurze](https://twitter.com/freekmurze?ref_src=twsrc%5Etfw) 搜索宏，这个版本拆分了搜索词，这样你就可以搜索“Sergio Bruder”，找到“Sergio Devojno Bruder”。搜索词在每个字段中进行“与”运算，在字段之间进行“或”运算。所以它不会找到“塞尔吉奥·埃尔斯”的名字和“【bruder@email.com】T2 的邮件。pic.twitter.com/YxvmRRw16P
> 
> —塞尔吉奥·布鲁德(@ sdbruder)[2018 年 10 月 20 日](https://twitter.com/sdbruder/status/1053719431602466816?ref_src=twsrc%5Etfw)

这是 Peter Matseykanets 的另一个版本。

> 我建议第四个？到
> 
> *   使名称更加明确(如果已经使用 Scout 会有帮助)
> *   通过不在术语周围强制使用%通配符来允许任意模式
> *   允许在相同或多个属性中搜索多个术语[pic.twitter.com/PKgdIBCa0G](https://t.co/PKgdIBCa0G)
> 
> —Peter Matseykanets([@ pmatseykanets](https://dev.to/pmatseykanets))[2018 年 10 月 19 日](https://twitter.com/pmatseykanets/status/1053381531962654721?ref_src=twsrc%5Etfw)

还有一个可以搜索软删除。

> 稍微调整了一下 [@freekmurze](https://twitter.com/freekmurze?ref_src=twsrc%5Etfw) 的搜索宏。这样，您还可以通过将 true 作为 whereLike 方法中的第三个参数来搜索软删除的条目。
> 
> 例如 Post::whereLike(['name '，' text '，'[https://t.co/kyWEu76VDt](https://t.co/kyWEu76VDt)']，$ searchTerm，true)->get()；pic.twitter.com/kSzvDxtPbW
> 
> –清真寺(@ theywillrepeat)[2018 年 10 月 31 日](https://twitter.com/TheyWillRepent/status/1057742260488585216?ref_src=twsrc%5Etfw)

如果您需要更高级的搜索，有许多选项。以下是其中的一些:

*   拉勒维尔侦察兵
*   [弹性搜索](https://www.elastic.co/products/elasticsearch)
*   [藻类](https://www.algolia.com)

寻找快乐！