# Laravel 5.4 中的“高阶消息”功能有多棒

> 原文：<https://dev.to/acro5piano/how-wonderful-feature-higher-order-messages-in-laravel-54--279>

我被移到了 5.4 版本增加的 Laravel 中的“高阶消息”功能。

[https://laravel.com/docs/5.5/collections#method-flatmap](https://laravel.com/docs/5.5/collections#method-flatmap)

我想举几个例子。

# 先决条件

有两种型号`Company`和`User`。

明显的关系是:

*   公司有许多用户
*   用户属于公司

`Company`模型有这些方法:

*   `getIsContractExpiredAttribute`
*   `notifyContractExpiration`

(这个例子有一些问题，但这只是一个例子)

然后，我们将随机抽取 5 家合同到期的公司，并向这些公司发送电子邮件。

# (~ 5.3)之前

```
Company::with('users')
    ->all()
    ->filter(function ($company) {
        return $company->isContractExpired;
    })
    ->take(5)
    ->pluck('users')->flatten()
    ->each(function ($user) {
        $user->notifyContractExpiration;
    }); 
```

Enter fullscreen mode Exit fullscreen mode

# (5.4 ~)后

```
Company::with('users')
    ->all()
    ->filter->isContractExpired
    ->take(5)
    ->flatMap->users
    ->each->notifyContractExpiration; 
```

Enter fullscreen mode Exit fullscreen mode

有更少的关闭。简单，可读性强。

# 明细

在上面的例子中，`Company::all()->map`返回有很多任务的`Illuminate\Support\HigherOrderCollectionProxy`。

以下方法支持`HigherOrderCollectionProxy`。

*   平均的
*   平均
*   包含
*   每个
*   每个
*   过滤器
*   第一
*   平面地图
*   地图
*   划分
*   拒绝
*   索提比
*   排序依据
*   总和

例如，`Company::all()->sum->isContractExpired`返回合同到期公司的数量。

SQL 之类的很多乐趣吧？