# ★在 Laravel 注册路线的更好方法

> 原文：<https://dev.to/freekmurze/a-better-way-to-register-routes-in-laravel-43j5>

让我们看看如何在 Laravel 中定义到控制器的路由。默认情况下，你可以这样做:

```
Route::get('my-route', 'MyController@index'); 
```

Enter fullscreen mode Exit fullscreen mode

这将在`App\Http\Controllers`名称空间中寻找`MyController`类。这个默认的名称空间是在 Laravel 的 RouteServiceProvider 中设置的[。](https://github.com/laravel/laravel/blob/dc3e768d4cb3c77056da6076f0e725327aa85ad1/app/Providers/RouteServiceProvider.php#L17)

这在很长一段时间内都没问题，但在现代 Laravel 应用程序(感谢公关，Jaanus Vapper)中，有一种很酷的新方法来注册路线。您可以使用元组来定义它们。让我们看看那是什么样子:

```
Route::get('my-route', [\App\Http\Controllers\MyController::class, 'index']); 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过导入名称空间来缩短它:

```
use App\Http\Controllers\MyController;

Route::get('my-route', [MyController::class, 'index']); 
```

Enter fullscreen mode Exit fullscreen mode

在现实世界的路由文件中，您可能会以大量的`use`语句结束，但是一个好的 IDE 会将它们全部隐藏起来。

为了让元组符号工作，你需要移除`RouteServiceProvider`中的[这个`namespace`调用](https://github.com/laravel/laravel/blob/dc3e768d4cb3c77056da6076f0e725327aa85ad1/app/Providers/RouteServiceProvider.php#L55)，这样 Laravel 就不会在一个已经完全合格的控制器类名前加上前缀`\App\Http\Controllers`。

与使用字符串相比，使用元组定义路由有两个好处:

1.  如果您使用的是像 PhpStorm 这样的 IDE，现在您只需点击路由文件中的控制器类名就可以找到正确的控制器。
2.  重构控制器的名称或命名空间时，路由文件中的类名也会改变。

使用`action`方法重定向时，也可以使用元组表示法。

```
namespace App\Http\Controllers;

class MyController
{
    public function myMethod()
    {
        // do some work...

        return redirect->action([MyOtherController::class, 'index']);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在使用一个可调用的控制器，你可以像这样定义一条路线

```
use App\Http\Controllers\MyController;

Route::get('my-route', MyController::class); 
```

Enter fullscreen mode Exit fullscreen mode

在 [Spatie](https://spatie.be) 我们现在在所有当前项目中使用这个元组符号。我也更新了我的博客来使用它们，这里是[的相关承诺](https://github.com/spatie/murze.be/commit/1da2a4f493074fb1a1606d181d90e57243905e19)。

在我看来，基于元组的路由应该成为 Laravel 未来版本的默认设置。只有好处，涉及的魔法更少(不需要在幕后给字符串加前缀)。

你喜欢这个符号吗？请在下面的评论中告诉我。