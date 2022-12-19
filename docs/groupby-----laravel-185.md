# Laravel 中多值的 GroupBy

> 原文：<https://dev.to/phpprofi/groupby-----laravel-185>

在 Laravel 中，从 5.5.29 开始，您可以根据多个条件对集合进行分组。让我们看看这意味着什么，以及它是如何工作的。

[![](img/f3414dc816f511a3f89a684b5cc07988.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TgbbA5Zq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://phpprofi.ru/resources/img/blogs/bf9c09d1-c041-48b9-9414-73b4d0464031.jpeg)

## t 0t 1 准备

所以这篇关于 Laravel 框架中新功能的文章。但是，在我们看看她现在的工作情况之前，让我们看看她以前的工作情况。T4 t0t 5 方法是 t6t 1 类 T7 方法。对于我们的例子，我将创建一点我们将要处理的数据。T2 将是传输的 T3 类的状态工厂。T8 型

```
// Default user factory which comes with Laravel
$factory->define(App\User::class, function (Faker $faker) {
    return [
        'name' => $faker->name,
        'email' => $faker->unique()->safeEmail,
        'password' => '$2y$10$TKh8H1.PfQx37YgCzwiKb.KjNyWgaHb9cbcoQgdIVFlYg7B77UdFm', // secret
        'remember_token' => str_random(10),
    ];
});

// Our new students state
$factory->state(App\User::class, 'students', function (Faker $faker) {
    return [
        'skilllevel' => collect(['beginner', 'intermediate', 'professional'])->random(),
        'teacher' => collect(['Peter', 'Markus', 'Chris'])->random(),
    ];
}); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

http://phpprofi.ru/blogs/post/97 的читать

* * *

en:[https://Christoph-rumpel . com/2018/01/group by-multi-levels-in-laravel](https://christoph-rumpel.com/2018/01/groupby-multiple-levels-in-laravel)