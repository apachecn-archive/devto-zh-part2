# Redis 和 Laravel 的速率限制 API 请求的简单示例

> 原文：<https://dev.to/eichgi/a-simple-example-of-rate-limiting-api-requests-with-redis-and-laravel-3d7i>

嗨，伙计们，我一直在参加 Redis 的课程，我必须说这是相当可怕的。Redis 在以简单易行的方式解决这些问题方面大放异彩。在这篇文章中，我将解释一个简单的例子，如何限制一个 API 可以接受的用户请求的数量。我们将利用拉勒维尔的优势。

第一步是创建一个**命令**，它允许我们建立一个用户每天可以消耗的配额。为此，我们将创建下一个命令:

```
php artisan make:command ResetQuotaForFreeUsers 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了命令，逻辑就是下一个:免费用户的配额将是 100 个请求。您可以将配额设置得更高，例如 5k，这只是一个示例。因此，如果我们的应用程序有 500 个注册的免费用户，那么代码将是:

```
class ResetQuotaForFreeUsers extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'reset:quotas';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'This command resets the quota to free users.';

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        for ($i = 1; $i <= 500; $i++) {
            Redis::command("hset", ["RateLimits", "User:{$i}:limit", 100]);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经建立了配额，可以开始接受请求了。

最近创建的命令可以用下一行执行:

```
php artisan reset:quotas 
```

Enter fullscreen mode Exit fullscreen mode

通常，当您使用第三个 API 时，您必须注册，无论您是否付费，您都会收到一对 API 密钥，它们由一个客户端密钥和一个秘密密钥组成(您可能只收到一个密钥，这完全取决于服务)。

对于这个例子，我们假设执行请求的用户 ID 是用户 50。你如何找到正确用户的逻辑取决于你是否在使用像 JWT、Laravel Passport 等服务

第二步是创建一个用于验证的**中间件**，执行下一行:

```
php artisan make:middleware CheckRateLimit 
```

Enter fullscreen mode Exit fullscreen mode

一旦创建了中间件，打开文件/app/Http/Kernel.php，然后在指定的数组中添加下一行:

```
protected $routeMiddleware = [
    ...
    'rate.limit' => \App\Http\Middleware\CheckRateLimit::class,
]; 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，您已经有了可用于验证的中间件。

中间件的逻辑很简单，你需要检查计数器是否仍然有效(大于 0)。我们将通过我们刚刚创建的中间件中的下一个验证来完成这个任务:

```
class CheckRateLimit
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        $id = 50;

        if (Redis::command('hincrby', ['RateLimits', "User:{$id}:limit", -1]) >= 0) {
            return $next($request);
        } else {
            return response()->json(['status' => 0, 'message' => 'You already reached your requests limit.']);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经设置了中间件，最后一部分是执行对特定路由的请求。我已经在 api routes 文件中创建了下一个**端点**用于测试和验证:

```
Route::get('/users', function () {
    $users = factory('App\User', 5)->make();
    return response()->json(['status' => 1, 'users' => $users], 200);
})->middleware('rate.limit'); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们通过工厂助手创建了 5 个用户，然后我们在 json 响应中返回他们，响应如下:

```
{  "status":  1,  "users":  [  {  "name":  "Brett Torphy",  "email":  "xander.konopelski@example.net"  },  {  "name":  "Catherine Mraz",  "email":  "rprice@example.com"  },  {  "name":  "Madalyn Williamson",  "email":  "ydubuque@example.net"  },  {  "name":  "Dr. Hillard Barton",  "email":  "schmeler.norma@example.com"  },  {  "name":  "Zola Turner",  "email":  "kristopher.jacobson@example.net"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

但是如果您用尽了所有的请求限制，那么响应将如下:

```
{  "status":  0,  "message":  "You already reached your requests limit."  } 
```

Enter fullscreen mode Exit fullscreen mode

现在您限制了用户可以对您的 API 执行的请求数量，恭喜您！

要考虑的规格:

你需要运行一个 redis 服务器，redis quickstart 文档:[https://redis.io/topics/quickstart](https://redis.io/topics/quickstart)
这个例子是在 redis 中使用散列，散列就像 PHP 中的数组，关于 redis 上的数据类型的文档可以在这里找到:[https://redis.io/topics/data-types](https://redis.io/topics/data-types)T5】你需要安装 predis 库以便在 PHP 中使用 redis，你可以在 packagist 上找到:[https://packagist.org/packages/predis/predis](https://packagist.org/packages/predis/predis)

如果你有任何建议或意见，请告诉我，我也在学习！

*本帖原载于 medium:[https://medium . com/@ eichgi/a-simple-example-of-rate-limiting-API-requests-with-redis-d 7 aabd 8 c 94 FB](https://medium.com/@eichgi/a-simple-example-of-rate-limiting-api-requests-with-redis-d7aabd8c94fb)T3】*