# 让您的工作对多租户友好

> 原文：<https://dev.to/lloople/make-your-laravel-jobs-multi-tenancy-friendly-1i99>

我们的团队正在努力工作的一个项目，可以连接到不同的数据库取决于客户。
这里是维基百科的链接:[多租户](https://en.wikipedia.org/wiki/Multitenancy):

> 术语“软件多租户”指的是一种软件架构，其中一个
> 软件实例运行在一台服务器上，为多个租户提供服务。

这里的要点是，我们只安装了一个应用程序，根据登录的用户，它会显示相应的数据。

当你发送一个任务到一个队列时，Laravel 使用你选择的驱动程序序列化这个类来保存它，直到一个工人可以启动它。
当作业被触发时，它试图使用
的`SerializesModel`特征从数据库中恢复您在构造函数中传递的模型。

这完全没问题，但有时你会得到一个重要的要求，这次轮到我们了。

我们没有在`config/databases.php`配置文件中为每个客户端定义连接。取而代之的是，我们在一个包含其他信息的`clients`表中使用了
数据库名称，并且每个`User`都有一个`client_id`来创建关系。
我们在每个请求的开始使用一个中间件来设置配置中的连接属性。

当我们开始使用 jobs 时，问题就出现了:由于没有客户端登录，所以无法确定必须建立哪个连接。经过深入的研究，并挖掘了框架的代码，我们找到了一个符合我们需求的
解决方案。

我将展示一些关于`database`驱动程序的例子，因为它是我们为应用程序选择的。但是我相信你可以对你选择的驱动程序进行同样的修改。

我们这里的目标是为每个作业设置它需要的连接，因此作业必须知道它属于哪个客户机。

这一步很简单:由于我们使用了数据库驱动程序，我们只需要运行以下命令:

```
php artisan queue:table 
```

然后，我们修改迁移文件，并在迁移前添加我们的“客户端 id”

```
Schema::create('jobs', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->string('queue')->index();
    $table->integer('client_id')->unsigned()->nullable();
    ...
}); 
```

但是，每当框架分派一个新任务时，它如何知道正确填充这个新字段的方法呢？这就是棘手的部分开始了:

在我们的`config/app.php`中，我们需要指定我们的客户`QueueServiceProvider`而不是`Illuminate`的:

```
'providers' => [
    ...
    Illuminate\Pipeline\PipelineServiceProvider::class,
    App\Queue\QueueServiceProvider::class,
    Illuminate\Redis\RedisServiceProvider::class,
    ... 
```

> 从现在开始，我们将在`App\Queues`名称空间中组织 Illuminate 队列的扩展类

这个提供者非常简单，它只需要扩展原来的类。

```
namespace App\Queue;

use App\Queue\Connectors\DatabaseConnector;

class QueueServiceProvider extends \Illuminate\Queue\QueueServiceProvider
{
    protected function registerDatabaseConnector($manager)
    {
        $manager->addConnector('database', function () {
            return new DatabaseConnector($this->app['db']);
        });
    }
} 
```

我们需要覆盖`DatabaseConnector`以使用我们自己的，这覆盖了`connect`以使用我们的`DatabaseQueue`

```
namespace App\Queue\Connectors;

use App\Queue\DatabaseQueue;

class DatabaseConnector extends \Illuminate\Queue\Connectors\DatabaseConnector
{

    public function connect(array $config)
    {
        return new DatabaseQueue(
            $this->connections->connection($config['connection'] ?? null),
            $config['table'],
            $config['queue'],
            $config['retry_after'] ?? 60
        );
    }
} 
```

现在我们需要覆盖方法`buildDatabaseRecord`来检查我们是否有客户端，如果有，
填充`jobs`表上的字段。

```
namespace App\Queue;

use App\Queue\Jobs\DatabaseJob;

class DatabaseQueue extends \Illuminate\Queue\DatabaseQueue
{

    protected function buildDatabaseRecord($queue, $payload, $availableAt, $attempts = 0)
    {
        $queueRecord = [
            'queue' => $queue,
            'attempts' => $attempts,
            'reserved_at' => null,
            'available_at' => $availableAt,
            'created_at' => $this->currentTime(),
            'payload' => $payload,
        ];

        if (client()) {
            $queueRecord['client_id'] = client()->id;
        }

        return $queueRecord;
    }
} 
```

好了，现在我们的工作已经分配了一个`client_id`,剩下的事情就是...
我们如何在作业被触发之前建立客户端连接？是的，你猜对了，
我们将需要为此扩展另一个照明类

```
namespace App\Queue\Jobs;

use App\Models\Client;
use App\Helpers\ClientConnector;

class DatabaseJob extends \Illuminate\Queue\Jobs\DatabaseJob
{
    public function fire()
    {
        if ($this->job->client_id) {
            $client = Client::findOrFail($this->job->client_id);

            ClientConnector::connectByClient($client);
        }

        parent::fire();
    }
} 
```

最后，我们需要返回到我们自己的`DatabaseQueue`，通过覆盖方法`marshalJob`，告诉它使用我们的`DatabaseJob`而不是 Illuminate 的队列默认值

```
protected function marshalJob($queue, $job)
{
    $job = $this->markJobAsReserved($job);

    return new DatabaseJob(
        $this->container, $this, $job, $this->connectionName, $queue
    );
} 
```