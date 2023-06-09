# ★介绍 laravel-event-projector:在 laravel 应用程序中开始活动采购的最简单方法

> 原文：<https://dev.to/freekmurze/introducing-laravel-event-projector-the-easiest-way-to-get-started-with-event-sourcing-in-a-laravel-app-8nj>

在大多数应用程序中，您将应用程序的状态存储在数据库中。如果需要更改，只需更新表中的值。

使用事件源时，您将采取不同的方法。对应用程序状态的所有更改都存储为一系列事件。主要好处是您现在有了数据库的历史记录。您可以使用这些存储的事件派生出应用程序需要的任何数据。

已经有几个写得非常好的 PHP 包，像 [prooph](https://github.com/prooph) 或 [EventSauce](https://eventsauce.io/) ，可以给你的应用程序带来事件源的力量。但是如果你从未从事过活动采购，那么开始使用这些包会很困难。

我觉得对于 Laravel 用户来说，应该有一个更容易的切入点来开始事件采购。这就是为什么在过去的几个月里，我花了一些时间创建了一个名为 [laravel-event-projector](https://github.com/spatie/laravel-event-projector) 的新包。它是几天前发行的。

这个包可以让你轻松地存储事件，创建投影和反应器(在这篇文章中你会学到更多)，回放事件等等。

在这篇文章中，我想向你介绍这个包。

## 传统例子

我们先用一个实际的例子让事件源背后的这个思想更清晰。假设你是一家银行，客户有账户。所有这些账户都有余额。当钱增加或减少时，我们可以修改余额。但是，如果我们这样做，我们将永远不知道为什么余额会达到那个数字。如果我们存储所有的事件，我们可以计算余额。

使用这些存储的事件，我们可以做一些其他有趣的事情。几年后，银行的经理们对上一年每个账户的平均余额感兴趣。如果您只存储余额本身，您将无法生成该报告。但是使用存储的事件，您可以很容易地生成这样的报告。

很可能你没有从零开始为银行实现账户平衡系统，所以上面的例子可能有点牵强。但是你可以在任何类型的项目中使用这个想法。想象你正在建立一个电子商务网站。您可以使用事件源构建购物车和订单的内容。对于这些事件，您可以生成许多报告。

## 介绍 laravel-event-projector

我制作了一个简短的视频来解释这个包的高级概念。

## 什么是投影仪？

在视频中，我提到了投影仪的概念。投影仪是一个在新事件到来时被触发的类。投影仪将数据写入存储器(可以是数据库或磁盘上的文件)。我们称这些书面数据为投影。让我们深入一些代码，学习如何编写一个投影仪。

假设你是一家银行，客户有账户。所有这些账户都有余额。当钱增加或减少时，我们可以修改余额。然而，如果我们这样做，我们将永远不会知道为什么余额会达到那个数字。如果我们将所有的交易存储为事件，我们可以计算余额。

### 创建模型

这里有一个小的迁移来创建一个存储帐户的表。使用一个`uuid`并不是严格要求的，但是当使用这个包时，它会使你的生活变得容易得多。在所有的例子中，我们都假设你会使用它们。

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
            $table->string('uuid');
            $table->string('name');
            $table->integer('balance')->default(0);
            $table->timestamps();
        });
    }
} 
```

`Account`模型本身可以是这样的:

```
namespace App;

use App\Events\AccountCreated;
use App\Events\AccountDeleted;
use App\Events\MoneyAdded;
use App\Events\MoneySubtracted;
use Illuminate\Database\Eloquent\Model;
use Ramsey\Uuid\Uuid;

class Account extends Model
{
    protected $guarded = [];

    protected $casts = [
        'broke_mail_send' => 'bool',
    ];

    public static function createWithAttributes(array $attributes): Account
    {
        /*
         * Let's generate a uuid. 
         */
        $attributes['uuid'] = (string) Uuid::uuid4();

        /*
         * The account will be created inside this event using the generated uuid.
         */
        event(new AccountCreated($attributes));

        /*
         * The uuid will be used the retrieve the created account.
         */
        return static::uuid($attributes['uuid']);
    }

    public function addMoney(int $amount)
    {
        event(new MoneyAdded($this->uuid, $amount));
    }

    public function subtractMoney(int $amount)
    {
        event(new MoneySubtracted($this->uuid, $amount));
    }

    public function delete()
    {
        event(new AccountDeleted($this->uuid));
    }

    /*
     * A helper method to quickly retrieve an account by uuid.
     */
    public static function uuid(string $uuid): ?Account
    {
        return static::where('uuid', $uuid)->first();
    }
} 
```

### 定义事件

我们只是触发事件，而不是创建、更新和删除账户。所有这些事件都应该实现`\Spatie\EventProjector\ShouldBeStored`。这是一个空接口，向我们的包表明事件应该被存储。

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
    /** @var string */
    public $accountUuid;

    /** @var int */
    public $amount;

    public function __construct(string $accountUuid, int $amount)
    {
        $this->accountUuid = $accountUuid;

        $this->amount = $amount;
    }
}

namespace App\Events;

use Spatie\EventProjector\ShouldBeStored;

class MoneySubtracted implements ShouldBeStored
{
    /** @var string */
    public $accountUuid;

    /** @var int */
    public $amount;

    public function __construct(string $accountUuid, int $amount)
    {
        $this->accountUuid = $accountUuid;

        $this->amount = $amount;
    }
}

namespace App\Events;

use Spatie\EventProjector\ShouldBeStored;

class AccountDeleted implements ShouldBeStored
{
    /** @var string */
    public $accountUuid;

    public function __construct(string $accountUuid)
    {
        $this->accountUuid = $accountUuid;
    }
} 
```

### 打造您的第一台投影仪

投影仪是一个监听存储的事件的类。当它听到感兴趣的事件时，它可以执行一些工作。

让我们创建您的第一台投影仪。您可以执行`php artisan make:projector AccountBalanceProjector`在`app\Projectors`中创建一个投影仪。

下面是一个处理上述所有事件的示例投影仪:

```
namespace App\Projectors;

use App\Account;
use App\Events\AccountCreated;
use App\Events\AccountDeleted;
use App\Events\MoneyAdded;
use App\Events\MoneySubtracted;
use Spatie\EventProjector\Models\StoredEvent;
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
        $account = Account::uuid($event->accountUuid);

        $account->balance += $event->amount;

        $account->save();
    }

    public function onMoneySubtracted(MoneySubtracted $event)
    {
        $account = Account::uuid($event->accountUuid);

        $account->balance -= $event->amount;

        $account->save();

        if ($account->balance >= 0) {
            $this->broke_mail_sent = false;
        }
    }

    public function onAccountDeleted(AccountDeleted $event)
    {
        Account::uuid($event->accountUuid)->delete();
    }
} 
```

### 注册您的投影仪

上面的投影仪代码将根据触发的事件更新`accounts`表。

投影仪需要注册。注册投影仪最简单的方法是调用`EventProjectionist`类中的`addProjector`。通常你会把它放在你自己的服务提供商那里。

```
use Illuminate\Support\ServiceProvider;
use App\Projectors\AccountBalanceProjector;
use Spatie\EventProjector\EventProjectionist;

class AppServiceProvider extends ServiceProvider
{
    public function boot(EventProjectionist $eventProjectionist)
    {
        $eventProjectionist->addProjector(AccountBalanceProjector::class);
    }
} 
```

也可以使用`EventProjectionist` facade。

```
use App\Projectors\AccountBalanceProjector;
use Spatie\EventProjector\Facades\EventProjectionist;

EventProjectionist::addProjector(AccountBalanceProjector::class); 
```

### 让我们开始一些活动吧

有了这些，我们就可以开始一些事件了。

让我们尝试添加一个帐户:

```
Account::createWithAttributes(['name' => 'Luke']);
Account::createWithAttributes(['name' => 'Leia']); 
```

让我们在这个账户上做一些交易:

```
$account = Account::where(['name' => 'Luke'])->first();
$anotherAccount = Account::where(['name' => 'Leia'])->first();

$account->addMoney(1000);
$anotherAccount->addMoney(500);
$account->subtractMoney(50); 
```

如果您看一下`accounts`表的内容，您应该会看到一些帐户以及它们的计算余额。太棒了。在`stored_events`表中，您应该看到我们触发的每个事件的条目。

## 您的第二台投影仪

想象一下，过了一段时间，银行里有人想知道哪些账户处理了最多的交易。因为我们将帐户的所有更改存储在 events 表中，所以我们可以通过创建另一个投影仪来轻松获取该信息。

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
            $table->string('account_uuid');
            $table->integer('count')->default(0);
            $table->timestamps();
        });
    }
} 
```

如果您正在跟进，不要忘记运行这个新的迁移。

```
php artisan migrate

namespace App;

use Illuminate\Database\Eloquent\Model;

class TransactionCount extends Model
{
    public $guarded = [];
} 
```

这是将要收听`MoneyAdded`和`MoneySubtracted`事件的投影仪:

```
namespace App\Projectors;

use App\Events\MoneyAdded;
use App\Events\MoneySubtracted;
use App\TransactionCount;
use Spatie\EventProjector\Models\StoredEvent;
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
        $transactionCounter = TransactionCount::firstOrCreate(['account_uuid' => $event->accountUuid]);

        $transactionCounter->count += 1;

        $transactionCounter->save();
    }

    public function onMoneySubtracted(MoneySubtracted $event)
    {
        $transactionCounter = TransactionCount::firstOrCreate(['account_uuid' => $event->accountUuid]);

        $transactionCounter->count += 1;

        $transactionCounter->save();
    }
} 
```

别忘了注册这台投影仪:

```
// in a service provider of your own
EventProjectionist::addProjector(TransactionCountProjector::class); 
```

如果你已经跟进，你已经创建了一些帐户和一些事件。为了将过去的事件输入到投影仪中，我们可以简单地执行这个 artisan 命令:

```
php artisan event-projector:replay 
```

该命令将获取存储在`stored_events`表中的所有事件，并将它们传递给`TransactionCountProjector`。命令完成后，您应该在`transaction_counts`表中看到事务计数。

### 迎接新的事件

现在你的两个投影都已经处理了所有的事件，试着触发一些新的事件。

```
Account::createWithAttributes(['name' => 'Yoda']); 
```

让我们向该帐户添加一些交易:

```
$yetAnotherAccount = Account::where(['name' => 'Yoda'])->first();

$yetAnotherAccount->addMoney(1000);
$yetAnotherAccount->subtractMoney(50); 
```

您会注意到两台投影仪都立即处理这些新事件。`Account`模型的余额是最新的，并且`transaction_counts`表中的数据也得到更新。

### 投影仪和投影的好处

投影仪很酷的一点是，你可以在事件发生后写出来。假设银行里有人想要一份每个账户平均余额的报告。您将能够编写一个新的投影仪，重放所有事件，并拥有这些数据。

投影查询速度非常快。我们的应用程序已经处理了数百万个事件。如果您想创建一个显示交易最多的账户的屏幕，您可以很容易地查询`transaction_counts`表。这样你就不需要发出一些昂贵的查询。投影仪将保持投影(`transaction_counts`表)最新。

## 用反应器处理副作用

既然你已经[编写了你的第一个投影仪](https://dev.to/laravel-event-projector/v1/basic-usage/writing-your-first-projector)，让我们来学习如何处理副作用。所谓副作用，我们指的是发送邮件，发送通知，...您只想在原始事件发生时执行这些操作。回放事件时，您不想做这项工作。

反应器是一个类，它很像一个投影仪，监听传入的事件。然而，与投影仪不同的是，当事件重放时，反应器不会被调用。只有当原始事件触发时，才会调用反应堆。

在上面的例子中，我们使用了雄辩模型作为我们投影的存储机制，但是你可以使用任何你想要的存储形式。但是请记住，您只能使用投影仪来改变投影。如果您要从另一个地方更新模型，这些更改将在重放事件时丢失。

### 创造你的第一个反应堆

让我们创建你的第一个反应堆。您可以执行`php artisan make:reactor BigAmountAddedReactor`在`app\Reactors`中创建一个反应器。每当账户上增加一大笔钱时，我们会让这个反应器向银行主管发送一封邮件。

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
    protected $handlesEvents = [
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

### 注册您的反应堆

为了让软件包能够找到反应堆，你应该注册它。注册投影仪最简单的方法是调用`EventProjectionist` facade 上的`addReactor`。通常你会把它放在你自己的服务提供商那里。

```
use \Spatie\EventProjector\Facades\EventProjectionist;
use \App\Reactor\BigAmountAddedReactor;

...

EventProjectionist::addReactor(BigAmountAddedReactor::class) 
```

### 使用电抗器

每当账户中增加 900 英镑或以上的金额时，上面的反应器就会向银行主管发送电子邮件。让我们启动反应堆。

```
$account = Account::createWithAttributes(['name' => 'Rey']);
$account->addMoney(1000); 
```

一封邮件将被送到主任那里。

如果您截断`accounts`表并用
重建内容

```
php artisan event-projector:replay 
```

不会发送任何邮件。

## 思考事件

图片你现在的任务是给一个破产的账户持有人发一封邮件。你可能会想，这很简单，如果账户余额小于零，我们就签入一个新的反应器。

我们先在`Account`模型中添加一个小小的 helper 方法，检查一个账号是否被破。

```
// ...

class Account extends Model

    // ...

    public function isBroke(): bool
    {
        return $this->balance < 0;
    }
} 
```

现在创建一个名为`BrokeReactor` :
的新反应器

```
namespace App\Reactors;

// ...

class BrokeReactor implements EventHandler
{
    use HandlesEvents;

    public $handlesEvents = [
        MoneySubtracted::class => 'onMoneySubtracted',
    ];

    public function onMoneySubtracted(MoneySubtracted $event)
    {
        $account = Account::uuid($event->accountUuid);

        if ($account->isBroke()) {
            Mail::to($account->email)->send(new BrokeMail($account));

            event(new BrokeMailSent($account->uuid));
        }
    }
} 
```

当一个帐户被打破的时候，一封邮件将会被送。这种方法的问题是，邮件还会发送给以前已经破产的帐户。如果你想只在一个账户从正结余变成负结余时发送邮件，我们需要做更多的工作。

您可能想在这里添加某种标志来确定邮件是否已经发送。

但是你不应该让 reactors 写你用投影仪建立的模型(或者你使用的任何存储机制)。如果你这样做，当重放事件时，所有的改变都将丢失:当重放事件时，事件不会被传递给反应器。请记住，反应堆是用来产生副作用的，而不是用来建立国家的。

如果你想修改一个反应堆的状态，只需触发一个新事件，让一个投影仪修改状态。让我们修改`BrokeReactor`来做到这一点。如果您一直在跟进，不要忘记创建将`broke_mail_sent`字段添加到`accounts`表的迁移。

```
// ...

class BrokeReactor implements EventHandler
{
    use HandlesEvents;

    public $handlesEvents = [
        MoneySubtracted::class => 'onMoneySubtracted',
        BrokeMailSent::class => 'onBrokeMailSent',
    ];

    public function onMoneySubtracted(MoneySubtracted $event)
    {
        $account = Account::uuid($event->accountUuid);

        /*
         * Don't send a mail if an account isn't broke
         */
        if (! $account->isBroke()) {
            return;
        }

        /*
         * Don't send a mail if it was sent already
         */
        if ($account->broke_mail_sent) {
            return;
        }

        Mail::to($account->email)->send(new BrokeMail($account));

        /*
         * Send out an event so the projector can modify the state.
         */
        event(new BrokeMailSent($account->uuid));
    }
} 
```

让我们利用`AccountBalanceProjector`中的新事件。

```
// ...

class AccountBalanceProjector implements Projector
{
    // ..

    public function onBrokeMailSent(BrokeMailSent $event)
    {
        $account = Account::uuid($event->accountUuid);

        $account->broke_mail_sent = true;

        $account->save();
    }

    public function onMoneyAdded(MoneyAdded $event)
    {
        $account = Account::uuid($event->accountUuid);

        $account->balance += $event->amount;

        /*
         * If the balance is above zero again, set the broke_mail_sent
         * flag to false again, so we can send another mail
         * when the balance goes below zero again.
         */
        if ($account->balance >= 0) {
            $account->broke_mail_sent = false;
        }

        $account->save();
    }
} 
```

有了这个，只有投影仪才能保存状态。只有当一个账户破产时,`BrokeReactor`才会发出一封邮件。如果帐户已经被打破，将不会发送任何邮件。当帐户超过零，再次破产，一封新的邮件将被发送。重播所有事件时，不会发送任何邮件，但所有帐户状态都是正确的。

## 处理队列中的事件

队列可以用来保证所有事件以正确的顺序传递给投影仪。如果您希望投影仪处理队列中的事件，您应该让您的投影仪实现`Spatie\EventProjector\Projectors\QueuedProjector`接口，而不是普通的`Spatie\EventProjector\Projectors\Projector`。这个接口只是向包暗示事件处理应该以排队的方式发生。

一个经验法则是，如果您的投影仪没有产生在事件被激发的同一请求中消耗的数据，您应该让您的投影仪实现`QueuedProjector`。

## 替代品

laravel-event-projector 可能是在 laravel 开始采购活动的最简单方式。它的~~紧密耦合~~完美集成，所以它使用雄辩的模型、本地队列系统，...如果您想在不太依赖 Laravel 的情况下获取事件源，可以考虑以下替代方案:

*   Prooph 是一个功能丰富的软件包套件。
*   FlySystem 的创造者 [Frank De Jonge](https://github.com/frankdejonge) 建造了 EventSauce 。

虽然 laravel-event-projector 非常实用，但上面的备选方案有点严格，提供了更多的概念，如聚合、cqrs，...

## 关闭

我希望你喜欢这次旅行。这篇博文的每一段代码都来自于一个演示应用，你可以在 GitHub 上的[这个报告中找到。](https://github.com/spatie/laravel-event-projector-demo-app)

如果你想了解更多关于活动采购的信息，这里有一些资源供你参考:

*   Kickstarter 的 Philippe Creux 的博客文章。有趣的事实:阅读这篇博客启动了我们的软件包的工作。
*   Michiel Rook 的演讲:处理事件源应用的变化
*   Barry O Sullivan 的博客

如果你喜欢 laravel-event-projector，看看我和我的团队创建的所有其他包。