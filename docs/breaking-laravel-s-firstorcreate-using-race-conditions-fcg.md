# ★利用比赛条件打破 Laravel 的首次创作

> 原文：<https://dev.to/freekmurze/breaking-laravel-s-firstorcreate-using-race-conditions-fcg>

最近，我在做一个客户项目，其中数据导入是通过队列执行的。每个记录都是使用多个队列工作线程导入到自己的队列作业中的。数据导入完成后，数据库中的行数比预期的多。在这篇博文中，我想解释一下为什么会这样。

在导入单个记录的排队作业中，正在使用`firstOrCreate`。如果我们查看这个本地 Laravel 函数的代码,我们会看到执行了两个查询。

```
/**
 * Get the first record matching the attributes or create it.
 *
 * @param array $attributes
 * @param array $values
 * @return \Illuminate\Database\Eloquent\Model
 */
public function firstOrCreate(array $attributes, array $values = [])
{
    if (! is_null($instance = $this->where($attributes)->first())) {
        return $instance;
    }
    return tap($this->newModelInstance($attributes + $values), function ($instance) {
        $instance->save();
    });
} 
```

首先，执行带有 where 条件的`select`查询来测试记录是否存在。如果是，则返回记录的模型实例。如果没有，我们就创建一个记录。

如果你不在队列中使用这个，并且你的应用程序上没有太多的流量，这就完美了。但是在排队的作业中，或者在高负荷的情况下，这可能会出错。如果作业 A 和 B 同时执行，那么这两个作业有可能同时执行`firstOrCreate`的第一个`select`查询。这两项工作都将得出记录尚不存在的结论。这两个作业将创建相同的记录，您最终会得到重复的记录。

你可能认为这是一个边缘案例。事实证明，这种问题的发生并不罕见。我创建了一个演示应用程序,它展示了你可以很快遇到这个问题。该应用程序有以下简单的命令来分派几个作业，这些作业将创建具有相同 uuid 的产品。

```
namespace App\Console\Commands;

use App\Jobs\CreateProductJob;
use Illuminate\Console\Command;
use Ramsey\Uuid\Uuid;
class CreateProductsCommand extends Command
{
    protected $signature = 'create-products';

    protected $description = 'Create some products';

    public function handle()
    {
        $uuid = (string) Uuid::uuid4();
        foreach(range(1, 5) as $i) {
            dispatch(new CreateProductJob($uuid));
        }
    }
} 
```

`CreateProductJob`看起来像这样:

```
namespace App\Jobs;

use App\Product;
use Illuminate\Bus\Queueable;
use Illuminate\Queue\SerializesModels;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
class CreateProductJob implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    /** @var string */
    public $uuid;

    public function __construct(string $uuid)
    {
        $this->uuid = $uuid;
    }
    public function handle()
    {
        Product::firstOrCreate(['uuid' => $this->uuid]);
    }
} 
```

默认情况下，该应用程序使用 Redis 队列和 Horizon。Horizon 配置为使用 3 个进程，因此可以并发调度 3 个作业。如果您启动该命令，您将会看到，即使有 5 个作业排队，我们最终也会重复记录。下面是实际演示:

[![](img/b46d1c40cb01580a673363e0f19378bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lwpuryWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://freek.dev/uploads/media/first-or-create/demo.gif)

我知道这个例子有点做作。我想强调的是，这个问题不是由 Redis、Horizon 或 Laravel 引起的。这是因为事情是并发执行的。在现实世界的项目中，您应该并且可能会在`uuid`列上放置一个唯一的索引，以防止您的数据库存储任何副本。在这篇文章中，我只想提醒你，如果多个作业向它传递相同的参数，那么让`firstOrCreate`产生意想不到的结果并不难。

要解决这个问题，有几种解决方案。您可以将队列的进程数量[设置为 1，这样就不会同时执行多个任务。如果您走这条路，我建议将作业调度到另一个队列上，这样默认的`queue`仍然可以使用多个进程。您也可以在数据库级别解决它。有一个`INSERT INTO .. ON DUPLICATE KEY`你可以利用。你可以在 GitHub](https://github.com/spatie/first-or-create-racing-condition-demo/blob/0e86acb362f34fc3399a5cc6690efe54ef0b2c0a/config/horizon.php#L89) 的[中找到一个例子。](https://gist.github.com/RuGa/5354e44883c7651fd15c#file-massinsertorupdate-php)

非常感谢我的同事亚历克斯，他帮助我们发现了客户项目中的这个问题，并审阅了这篇文章。你有别的策略来避免这个问题吗？请在下面的评论中告诉我。