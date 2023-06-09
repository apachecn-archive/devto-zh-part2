# 揭开 Laravel 队列的神秘面纱

> 原文：<https://dev.to/idoko/demystifying-laravel-queues-2ec3>

在某些时候，您的应用程序将需要监听线索并为它们发出一个事件。这些提示可能是生日或约会提醒，或者是 saas 应用试用版用户的到期提醒。不出所料，Laravel 有一个内置的方式来优雅地处理这个问题。在这里，我们将在试用期到期前 15 天向使用我们神奇的 saas 应用程序的用户发送提醒。

### 首先...司机

与 Laravel 尽可能多地包含电池(这肯定是好的或坏的)的声誉相符，该框架提供了对不同队列处理程序的开箱即用支持，包括 redis、beanstalk、亚马逊 SQS，当然还有我们将在这里使用的关系数据库。这里还有一个关于队列驱动程序[的令人惊奇的文档](https://laravel.com/docs/5.6/queues#driver-prerequisites),所以请随意探索它们。
要使用数据库驱动程序，我们需要在应用程序数据库中创建一个表来保存作业，laravel 为此提供了一个干净的助手命令。因此，我们将创建并运行必要的迁移，如下所示:

```
php artisan queue:table
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

### 潜入

我们想让我们的工作具备在后台持续运行的能力，所以我们将创建一个新的 artisan 命令来触发任务。Artisan 命令是 laravel 扩展已经很强大的命令行界面的方式，这里有大量关于它的文档。创建一个新的就像运行一样简单:

```
php artisan make:command SendExpiryCommand 
```

Enter fullscreen mode Exit fullscreen mode

这在`app/Console/Commands`中生成了一个新类。在使用之前，我们将修改这个新类的`signature`和`description`属性以及`handle()`方法。
`signature`是我们从 CLI 中调用的，其格式为`command:name`，- think `db:seed`，`config:clear`等，因此，让我们将签名改为`trial_expiry:notify`，并将描述改为更具描述性的内容。现在，当我们运行闪亮的命令时，就会调用`handle()`方法，这也是我们的代码(或者大部分代码)将存在的地方。现在，我们将假设我们的应用程序有一个`Trials`模型，该模型有一个`expires_in`字段保存试用期应该到期的日期，还有一个`user_id`字段引用我们的用户模型。下面是我们最终的 handle()代码:

```
/**
* Execute the console command.
*
* @return mixed
*/
public function handle(){
    //get the date of 15 days from now
    $actualDate = Carbon::now()->addDays(15);
    $actualDate = $actualDate->format('Y-m-d');
    $candidates = Trial::where('expires_in', '=', $actualDate)->get();      
    foreach ($candidates as $candidate) {
        SendExpiryNotification::dispatch($candidate);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`SendExpiryNotification`是实际的可排队作业，但是它还不存在，所以让我们创建它:

```
php artisan make:job SendExpiryNotification 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将在`app/Jobs`中创建一个新的类，该类实现了一个`ShouldQueue`来指示该作业将被异步运行。如果 Jobs 文件夹还不存在也不用担心，`artisan`会创建一个。在新的类中，我们遇到了另一个`handle()`方法，它类似于命令，是我们运行`dispatch`时调用的类的调味汁，也是我们的代码所在的地方。嘿，你好！现在，我们通过一个试用实例而不是电子邮件的一些原因可能是显而易见的，但是除了你头脑中的东西之外，我们需要一个我们雄辩模型的新实例，然后从队列中运行它，这正是它所做的。雅各布·班纳特有一篇令人惊叹的[博客文章](https://gistlog.co/JacobBennett/e60d6a932db98985f160146b09455988)详细介绍了这一点。此外，我们的 handle 方法不会直接将试用实例作为参数，而是将实例提供给作业构造函数，并从处理程序中调用它。
说够了，给我看看代码！好的，这是我们的`SendExpiryNotification`工作的样子

```
<?php

namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Queue\SerializesModels;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Support\Facades\Mail;

class SendExpiryNotification implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public $trial;

    public function __construct($candidate) {
        $this->trial = $candidate;
    }

    public function handle()
    {
        $user = User::find($this->trial->user_id);
        Mail::raw('Hello, Your trial would be expiring soon and we wanted you to know!', function($message) {
            $message->to($user->email);
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### ...坚持下去

现在大部分已经完成，让我们~~看看~~让它工作。我们将以
开始我们的队列监听

```
php artisan queue:listen 
```

Enter fullscreen mode Exit fullscreen mode

或者:

```
php artisan queue:work --daemon 
```

Enter fullscreen mode Exit fullscreen mode

虽然普遍的看法是第二种效果更好。因此，当我们的队列监听器仍在运行时，我们使用命令签名启动我们的命令，在本例中是

```
php artisan trial_expiry:notify 
```

Enter fullscreen mode Exit fullscreen mode

很简单，不是吗？哦，顺便说一下，[这里的](https://laravel.com/docs/5.6/queues)是 laravel 的队列文档，如果你是那种人的话。