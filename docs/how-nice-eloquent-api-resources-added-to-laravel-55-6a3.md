# 多好的雄辩的 API 资源，添加到 Laravel 5.5

> 原文：<https://dev.to/acro5piano/how-nice-eloquent-api-resources-added-to-laravel-55-6a3>

这是一篇有点晚的文章，但是我发现 Laravel 5.5 的雄辩的 API 资源是一个非常好的特性。

# 何时使用雄辩的 API 资源

当我们用 Laravel 创建一个基本的 REST API 时，最简单的代码应该是这样的:

```
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller as BaseController;
use App\Company;

class CompanyController extends BaseController
{
    public function index()
    {
        return Company::all();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

(我们假设`Company`是这里的主要领域参与者)

Laravel 将`Company::all()`作为`Company`的数组返回。

但是我们有时需要返回更复杂的数据。

假设`Company`有很多属性，您应该根据请求改变格式。

例如，`Company`有一些类似`phone_number`的敏感信息，只有`admin`用户可以查看。

也许我们应该写一个逻辑来决定这样的请求:

```
 public function index(Request $request)
    {
        return Company::all()->map(function ($company) use ($request) {
            if (! $request->user->is_admin) {
                unset($company['phone_number']);
            }
        });
    } 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以将`phone_number`添加到`Company`模型中的`hidden`属性中。
无论你做什么，你都应该用程序的方式在控制器中定义一些逻辑。

上面的例子还是比较容易的。在现实世界的项目中，我们应该返回更复杂的数据。(不同时间，包括其他关系，组合字符串，...)

这就是雄辩的 API 资源发挥作用的地方。

# 变换图层

当我们需要一个复杂的数据结构，甚至是简单的数据结构时，“转换层”就很有用。

这一层只是接收数据并返回一个数组。

[![](../Images/8c2f0b1cb738a92d447b4de1d3ca7363.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bqWHRIwh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/au60qh8hc2afux49sx62.jpg)

它的优点是:

*   保持控制器简单
*   用它的助手化简逻辑
*   易于理解的数据结构 API 服务器返回

我觉得最后一条真的很重要。
如果没有这一层，我们应该检查雄辩模型或表格定义、`$append`属性或控制器程序。

(如果您的团队创建了完整的最新 API 文档，您可以参考它。但是在很多项目中你可能没有时间去写它...！)

# 替换代码

在上面的例子中，我们在控制器中编写转换逻辑。让我们把逻辑移到`CompanyResource`。

```
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\Resource;

class CompanyResource extends Resource
{
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'phone_number' => $this->when($request->user->is_admin, $this->phone_number),
        ];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们会发现结构变得更加清晰。即使是前端工程师也可以很容易地找到什么/什么时候返回属性。

让我们在控制器中使用它。

```
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller as BaseController;
use App\Company;
use App\Http\Resources\CompanyResource;

class CompanyController extends BaseController
{
    public function index()
    {
        return CompanyResource::collection(
            Company::all()
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

控制器变得非常简单，将转换逻辑委托给了`CompanyResource`。

`when`由雄辩资源提供。简单但强大的助手，因为它允许我们编写声明性代码。

你可以使用很多雄辩资源的有用功能。查看官方文件了解更多信息。

[https://laravel.com/docs/5.5/eloquent-resources](https://laravel.com/docs/5.5/eloquent-resources)

顺便说一句，Laravel 的文档真的很棒，我们可以在那里找到几乎所有我们需要的东西。

# 分形为 Larave ~5.4

如果你用的是 Laravel ~5.4，我推荐用分形。

它是一个很棒的“转换层”库，并提供了 Laravel 包装器。

[https://github.com/spatie/laravel-fractal](https://github.com/spatie/laravel-fractal)

注意:即使在 5.5 中也可以使用分形。它有很多特点。

# 再者

你可能还记得 GraphQL。它使我们能够比 REST 更流畅地构建 API 服务器。

我们可以将 GraphQL 与下面的库一起使用。

[https://github.com/Folkloreatelier/laravel-graphql](https://github.com/Folkloreatelier/laravel-graphql)

我还没有试过，所以我会尽快检查。