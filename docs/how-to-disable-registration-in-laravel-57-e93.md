# 如何在 Laravel 5.7 中禁用注册

> 原文：<https://dev.to/chhedamilan/how-to-disable-registration-in-laravel-57-e93>

[![[How-to] Disable Registration in Laravel 5.7](../Images/8b3539779f6266812f7d94e68be70b60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C8PLfo5L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://milanchheda.com/uploads/cards/17.png)

在我之前的[帖子](https://dev.to/chhedamilan/how-to-disable-registration-in-laravel-1pk3-temp-slug-8145473)中，我已经展示了如何在 Laravel 中禁用注册。

在 Laravel 5.7 中，引入了一种禁用注册的新方法，这都要归功于[丹尼斯·斯明克](https://twitter.com/dennis_smink)，他出色的[拉请求](https://github.com/laravel/framework/pull/25556)

```
Auth::routes(['register' => false]); 
```

这是您需要添加的唯一代码，它将禁用您的 Laravel 应用程序中的注册。