# ★让在拉勒维尔寻找活动资源变得轻而易举的套餐

> 原文：<https://dev.to/freekmurze/a-package-that-makes-event-sourcing-in-laravel-a-breeze-koa>

在大多数应用程序中，您将应用程序的状态存储在数据库中。如果需要更改某些内容，只需更新表中的值。

使用事件源时，您将采取不同的方法。对应用程序状态的所有更改都存储为一系列事件。这里的主要好处是您现在有了数据库的历史记录。

让我们用一个实际的例子来更清楚地说明这个想法。假设你是一家银行，客户有账户。所有这些账户都有余额。当钱增加或减少时，我们可以修改余额。但是，如果我们这样做，我们将永远不知道为什么余额会达到那个数字。如果我们存储所有的事件，我们可以计算余额。

使用存储的事件，我们可以做一些其他有趣的事情。几年后，银行的经理们对上一年每个账户的平均余额感兴趣。如果您只存储余额本身，您将无法生成该报告。但是使用存储的事件，您可以很容易地生成这样的报告。

## 事件采购入门

有几个很酷的选择可以从事件采购开始，如[proof](https://github.com/prooph)或 [EventSauce](https://eventsauce.io/) 。这些是非常强大的软件包。当你是活动采购的新手时，他们也有点难以上手。

我觉得对于 Laravel 用户来说，应该有一个更容易的切入点来开始事件采购。这就是为什么我目前正在构建一个名为 [laravel-event-projector](https://github.com/spatie/laravel-event-projector) 的新包。它与 Laravel 完美融合。它允许用户轻松地存储事件，创建投影和反应器(你将在这篇文章中进一步了解这些东西)，重放事件等等。

在这篇文章中，我想给你一个如何使用它的例子。在 GitHub 上的[这个回购中，你会在关注这个帖子后找到完成的应用。](https://github.com/spatie/laravel-event-projector)

## 让我们安装软件包

`laravel-event-projector`可以通过 Composer 安装到 Laravel app:

```
composer require spatie/laravel-event-projector 
```

您需要发布并运行迁移来创建`stored_events`和`projector_statuses_tables` :

```
php artisan vendor:publish --provider="Spatie\EventProjector\EventProjectorServiceProvider" --tag="migrations"
php artisan migrate 
```

这样一来，我们就可以开始使用这个包了。

## 书写你的第一个投影

让我们来学习如何编写投影仪。投影仪只是一个类，当它听到一些事件进来时做一些工作。通常，它会写入数据(写入数据库或磁盘上的文件)。我们称这些书面数据为投影。

假设你是一家银行，客户有账户。所有这些账户都有余额。当钱增加或减少时，我们可以修改余额。但是，如果我们这样做，我们将永远不知道为什么余额会达到那个数字。如果我们存储所有的事件，我们可以计算余额。

## 创建模型

这里有一个小的迁移来创建一个保存账户的表:

```
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateAccountsTable extends Migration
{
    public function up()
    {
        Schema::create('accounts', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->integer('balance')->default(0);
            $table->timestamps();
        });
    }
} 
```

`Account`模型本身可以是这样的:

```
use App\Events\AccountCreated;
use App\Events\AccountDeleted;
use App\Events\MoneyAdded;
use App\Events\MoneySubtracted;
use Illuminate\Database\Eloquent\Model;

class Account extends Model
{
    protected $guarded = [];

    public static function createWithAttributes(array $attributes)
    {
        event(new AccountCreated($attributes));
    }

    public function addMoney(int $amount)
    {
        event(new MoneyAdded($this->id, $amount));

        return $this;
    }

    public function subtractMoney(int $amount)
    {
        event(new MoneySubtracted($this->id, $amount));

        return $this;
    }

    public function close()
    {
        event(new AccountDeleted($this->id));
    }
} 
```

## 定义事件

我们不是计算平衡，而是简单地发射事件。所有这些事件都应该实现`\Spatie\EventProjector\ShouldBeStored`。这是一个空接口，向我们的包表明事件应该被存储。

让我们看看在`Account`模型中使用的所有事件。

```
namespace App\Events;

use Spatie\EventProjector\ShouldBeStored;

class AccountCreated implements ShouldBeStored
{
    /** @var array */
    public $accountAttributes;

    public function __construct(array $accountAttributes)
    {
        $this->accountAttributes = $accountAttributes;
    }
}

namespace App\Events;

use Spatie\EventProjector\ShouldBeStored;

class MoneyAdded implements ShouldBeStored
{
    /** @var int */
    public $accountId;

    /** @var int */
    public $amount;

    public function __construct(int $accountId, int $amount)
    {
        $this->accountId = $accountId;

        $this->amount = $amount;
    }
}

namespace App\Events;

use Spatie\EventProjector\ShouldBeStored;

class MoneySubtracted implements ShouldBeStored
{
    /** @var int */
    public $accountId;

    /** @var int */
    public $amount;

    public function __construct(int $accountId, int $amount)
    {
        $this->accountId = $accountId;

        $this->amount = $amount;
    }
}

namespace App\Events;

use Spatie\EventProjector\ShouldBeStored;

class AccountDeleted implements ShouldBeStored
{
    /** @var int */
    public $accountId;

    public function __construct(int $accountId)
    {
        $this->accountId = $accountId;
    }
} 
```

## 打造您的第一台投影仪

投影仪是一个监听存储的事件的类。当它听到感兴趣的事件时，它可以执行一些工作。

让我们创建您的第一台投影仪。您可以执行`php artisan make:projector AccountBalanceProjector`在`app\Projectors`中创建一个投影仪。

下面是一个处理上述所有事件的示例投影仪:

```
namespace App\Projectors;

use App\Account;
use App\Events\AccountCreated;
use App\Events\AccountDeleted;
use App\Events\MoneyAdded;
use Spatie\EventProjector\Projectors\Projector;
use Spatie\EventProjector\Projectors\ProjectsEvents;

class AccountBalanceProjector implements Projector
{
    use ProjectsEvents;

    /*
     * Here you can specify which event should trigger which method.
     */
    public $handlesEvents = [
        AccountCreated::class => 'onAccountCreated',
        MoneyAdded::class => 'onMoneyAdded',
        MoneySubtracted::class => 'onMoneySubtracted',
        AccountDeleted::class => 'onAccountDeleted',
    ];

    public function onAccountCreated(AccountCreated $event)
    {
        Account::create($event->accountAttributes);
    }

    public function onMoneyAdded(MoneyAdded $event)
    {
        $account = Account::find($event->accountId);

        $account->balance += $event->amount;

        $account->save();
    }

    public function onMoneySubtracted(MoneyAdded $event)
    {
        $account = Account::find($event->accountId);

        $account->balance -= $event->amount;

        $account->save();
    }

    public function onAccountDeleted(AccountDeleted $event)
    {
        Account::find($event->accountId)->delete();
    }
} 
```

## 注册您的投影仪

上面的投影仪代码将处理给定事件需要完成的所有工作。为了使软件包能够找到此投影仪，您应该注册它。注册投影仪最简单的方法是调用`EventProjectionist` facade 上的`addProjector`。通常你会把它放在你自己的服务提供商那里。

```
use \Spatie\EventProjector\Facades\EventProjectionist;
use \App\Projectors\AccountBalanceProjector;

...

EventProjectionist::addProjector(AccountBalanceProjector::class) 
```

## 让我们开始一些活动吧

有了这些，我们就可以开始一些事件了。

让我们尝试添加一个帐户:

```
$account = Account::createWithAttributes(['name' => 'Luke']);
$anotherAccount = Account::createWithAttributes(['name' => 'Leia']); 
```

让我们在这个账户上做一些交易:

```
// do stuff like this

$account->addMoney(1000);
$anotherAccount->addMoney(500);
$account->subtractMoney(50);

... 
```

如果您看一下`accounts`表的内容，您应该会看到一些帐户以及它们的计算余额。太棒了。在`stored_events`中，您应该看到我们触发的每个事件的条目。

## 您的第二台投影仪

想象一下，过了一会儿，银行里有人想知道哪个账户处理了最多的交易。因为我们将帐户的所有更改存储在 events 表中，所以我们可以通过创建另一个投影仪来轻松获取该信息。

我们将创建另一个投影仪，在模型中存储每个帐户的交易计数。请记住，您可以轻松地使用任何其他存储机制来代替模型。投影仪不在乎你用什么。

下面是投影仪将要使用的迁移和模型类:

```
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateTransactionCountsTable extends Migration
{
    public function up()
    {
        Schema::create('transaction_counts', function (Blueprint $table) {
            $table->increments('id');
            $table->integer('account_id');
            $table->integer('count')->default(0);
            $table->timestamps();
        });
    }
}

namespace App;

use Illuminate\Database\Eloquent\Model;

class TransactionCount extends Model
{
    public $guarded = [];
} 
```

这是将要收听`MoneyAdded`和`MoneySubtracted`事件
的投影仪

```
namespace App\Projectors;

use App\Events\MoneyAdded;
use App\Events\MoneySubtracted;
use App\TransactionCount;
use Spatie\EventProjector\Projectors\Projector;
use Spatie\EventProjector\Projectors\ProjectsEvents;

class TransactionCountProjector implements Projector
{
    use ProjectsEvents;

    public $handlesEvents = [
        MoneyAdded::class => 'onMoneyAdded',
        MoneySubtracted::class => 'onMoneySubtracted',
    ];

    public function onMoneyAdded(MoneyAdded $event)
    {
        $transactionCounter = TransactionCount::firstOrCreate(['account_id' => $event->accountId]);

        $transactionCounter->count +=1;

        $transactionCounter->save();
    }

    public function onMoneySubtracted(MoneySubtracted $event)
    {
        $transactionCounter = TransactionCount::firstOrCreate(['account_id' => $event->accountId]);

        $transactionCounter->count -=1;

        $transactionCounter->save();
    }
} 
```

别忘了注册这台投影仪:

```
// in a service provider of your own
EventProjectionist::addProjector(TransactionCountProjector::class) 
```

如果你已经跟进，你已经创建了一些帐户和一些事件。为了将过去的事件输入到投影仪中，我们可以简单地执行 artisan 命令:

```
php artisan event-projector:replay-events 
```

该命令将获取存储在`stored_events`表中的所有事件，并将它们传递给`TransactionCountProjector`。命令完成后，您应该在`transaction_counts`表中看到事务计数。

## 迎接新的事件

现在你的两个投影都已经处理了所有的事件，试着触发一些新的事件。

```
$yetAnotherAccount = Account::createWithAttributes(['name' => 'Yoda']); 
```

让我们向该帐户添加一些交易:

```
// do stuff like this

$yetAnotherAccount->addMoney(1000);
$yetAnotherAccount->subtractMoney(50); 
```

你会注意到两台投影仪都在工作。`Account`模型的余额是最新的，并且`transaction_counts`表中的数据也得到更新。

## 投影仪和投影的好处

投影仪很酷的一点是，你可以在事件发生后编写它们。想象一下，银行里有人想要一份过去一年每个账户平均余额的报告。您将能够编写一个新的投影仪，重放所有事件，并让该投影仪生成这样的报告。

投影查询速度非常快。我们的应用程序已经处理了数百万个事件。如果您想创建一个显示交易最多的账户的屏幕，您可以很容易地查询`transaction_counts`表。这样你就不需要发出一些昂贵的查询。投影仪将保持投影(`transaction_counts`表)最新。

## 利用反应器处理副作用

现在你已经写好了你的第一个投影仪，让我们来学习如何处理副作用。所谓副作用，我们指的是发送邮件，发送通知，...您只想在原始事件发生时执行这些操作。回放事件时，您不想做这项工作。

反应器是一个类，它很像一个投影仪，监听传入的事件。然而，与投影仪不同的是，当事件重放时，反应器不会被调用。只有当原始事件触发时，才会调用反应堆。

## 创造你的第一个反应堆

让我们创建你的第一个反应堆。您可以执行`php artisan make:reactor BigAmountAddedReactor`在`app\Reactors`中创建一个投影仪。每当账户上增加一大笔钱时，我们会让这个反应器向银行主管发送一封邮件。

```
namespace App\Reactors;

use App\Account;
use App\Events\MoneyAdded;
use App\Mail\BigAmountAddedMail;
use Illuminate\Support\Facades\Mail;

class BigAmountAddedReactor
{
    /*
     * Here you can specify which event should trigger which method.
     */
    public $handlesEvents = [
        MoneyAdded::class => 'onMoneyAdded',
    ];

    public function onMoneyAdded(MoneyAdded $event)
    {
        if ($event->amount < 900) {
            return;
        }

        $account = Account::find($event->accountId);

        Mail::to('director@bank.com')->send(new BigAmountAddedMail($account, $event->amount));
    }
} 
```

## 注册您的反应堆

为了让软件包能够找到反应堆，你应该注册它。注册投影仪最简单的方法是调用`EventProjectionist` facade 上的`addReactor`。通常你会把它放在你自己的服务提供商那里。

```
use \Spatie\EventProjector\Facades\EventProjectionist;
use \App\Reactor\BigAmountAddedReactor;

...

EventProjectionist::addReactor(BigAmountAddedReactor::class) 
```

## 使用电抗器

每当账户中增加 900 英镑或以上的金额时，上面的反应器就会向银行主管发送电子邮件。让我们启动反应堆。

```
$account = Account::createWithAttributes(['name' => 'Rey']);
$account->addMoney(1000); 
```

一封邮件将被送到主任那里。

如果您截断`accounts`表并用
重建内容

```
php artisan event-projector:replay-events 
```

不会发送任何邮件。

## 关闭思绪

我希望你喜欢这次旅行。如上所述，你可以在 GitHub 上的[这个报告中找到一个 Laravel 应用程序，里面有这篇文章的所有例子。](https://github.com/spatie/laravel-event-projector-demo-app)

在接下来的几天和几周内，我将致力于发布一个稳定的软件包版本。如果你想玩玩它，它应该已经很稳定了，但是请记住，它肯定还没有准备好投入生产。我可能还是会做出突破性的改变。如果您对此有任何疑问，请随时在[laravel-event-projector repo](https://github.com/spatie/laravel-event-projector)上提出问题。