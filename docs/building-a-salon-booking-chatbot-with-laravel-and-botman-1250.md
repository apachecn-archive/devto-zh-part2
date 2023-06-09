# 使用 BotMan 构建预订聊天机器人

> 原文：<https://dev.to/devkiran/building-a-salon-booking-chatbot-with-laravel-and-botman-1250>

BotMan 是 PHP 最流行的聊天机器人开发框架。您可以在现有的代码库中或任何 PHP 框架中使用 BotMan。

有不同的 chatbot 开发框架可用于 Node Js、Python。BotMan 是唯一一个真正帮助开发者使用 PHP 开发聊天机器人的 PHP 框架。

如果你是一个 Laravel 开发者，BotMan Studio 是你正在寻找的东西。BotMan Studio 是 BotMan 和 Laravel 的捆绑版本。

如何使用机器人创建聊天机器人？

我们来看看 BotMan Studio，为网站搭建一个简单的沙龙预约 chatbot。

# 安装并设置 BotMan Studio

首先，确保您的系统上安装了 composer。现在使用 Composer 下载 BotMan 安装程序。

```
composer global require "botman/installer" 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的系统上安装 BotMan Studio。

确保放置$HOME/。$PATH 中的 composer/vendor/bin 目录(或您的操作系统的等效目录),以便您的系统可以找到 botman 可执行文件。

在命令行中键入“botman ”,查看安装是否正常。如果你能看到类似下图的输出，那么 BotMan Studio 安装是成功的。

# 创建新的聊天机器人项目

现在让我们使用 BotMan Studio 来创建新的聊天机器人。您可以使用“botman new”命令创建一个全新的 BotMan Studio 安装。

```
botman new salon 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将创建一个名为“salon”的新目录，其中安装了 BotMan Studio，并且已经安装了 BotMan 的所有依赖项。

# 测试安装工程

现在使用
启动开发服务器

```
cd salon
php artisan serve 
```

Enter fullscreen mode Exit fullscreen mode

这将在 [http://127.0.0.1:8000](http://127.0.0.1:8000) 启动开发服务器

如果安装没问题，您可以看到 Botman 的欢迎页面。

点击链接“Tinker”，在那里你可以尝试一个非常简单的聊天机器人。对机器人说“嗨”，机器人会回复“你好！”。

[![BotMan chatbot development](img/fefb8e56d40cb96925388d47c15f9d87.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IRnZtgvY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preview.ibb.co/gq6g0U/qmrwa0sunw73g6upzx3s.png)

干得好。现在，您已经为构建第一个聊天机器人做好了一切准备。

# 安装 web 驱动程序

BotMan 为开发者提供了许多驱动程序。驱动程序帮助我们将机器人与 Facebook Messenger、Slack、Telegram、Twilio、Web 等消息渠道连接起来。

因为我们要构建一个基于 web 的聊天机器人，所以让我们安装 web 驱动程序。

```
php artisan botman:install-driver web 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用 WebDriver 将聊天机器人添加到我们的网站，或者使用 API。Web 驱动程序附带了用于发送和接收消息的 API。这有助于我们利用 WebDriver APIs 构建自己的聊天界面。

BotMan 附带了一个可以开箱即用的 web 小部件。我们可以将 Javascript 片段包含到我们的欢迎视图中(welcome.blade.php)

```
<script src='https://cdn.jsdelivr.net/npm/botman-web-widget@0/build/js/widget.js'></script> 
```

Enter fullscreen mode Exit fullscreen mode

现在，刷新您的页面，您可以在页面的右下角看到聊天小部件。对机器人说“嗨”，机器人会回复“你好！”。就是这样。

[![BotMan chat widget](img/d534eb703b2dce5e40fba5b0ca318cf1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G8HVLosI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preview.ibb.co/gOGQS9/yafe5vs55wpe1kzvbzts.png)

在开始使用 BotMan Studio 构建聊天机器人之前，有两个重要的核心概念需要学习——听到消息和发送消息。

像任何其他 web 应用程序一样，chatbot 接收一些请求，处理它并将响应发送回通道(Web、Messenger、Slack 等)。

# 听到消息

```
$botman = resolve('botman');

$botman->hears('Hi', function ($bot) {
    $bot->reply('Hello!');
}); 
```

Enter fullscreen mode Exit fullscreen mode

你甚至可以使用如下的正则表达式。它会在传入消息的任何地方监听“Hi”或“Hello”。

```
$botman = resolve('botman');

$botman->hears('._(Hi|Hello)._', function ($bot) {
    $bot->reply('Hello!');
}); 
```

Enter fullscreen mode Exit fullscreen mode

代替闭包，你可以传递一个类和方法，如果关键字匹配，它们将被调用。在下面的例子中，如果用户说“嗨”，将调用 startConversation 方法。

```
$botman = resolve('botman');

$botman->hears('._(Hi|Hello)._', BotManController::class.'@startConversation'); 
```

Enter fullscreen mode Exit fullscreen mode

# 发送消息

您已经看到了如何使用 BotMan 发送消息。机器人可以用不同的方式发送信息——文本、按钮、附件、通用模板等。

让我们看看如何用两个按钮发送短信。

```
$botman = resolve('botman');

$question = Question::create('Do you agree with me?')
    ->callbackId('agree')
    ->addButtons([
        Button::create('Yes')->value('yes'),
        Button::create('No')->value('no'),
    ]);

$botman->ask($question, function(Answer $answer) {

}); 
```

Enter fullscreen mode Exit fullscreen mode

# 聊天机器人对话流程

我们将建立一个简单的沙龙预订聊天机器人，帮助网站访问者通过聊天机器人从他们的网站预订沙龙服务。

每个聊天机器人必须有一个或多个对话流。这是我们将要构建的聊天机器人的预期对话流。

*   欢迎来访者
*   询问姓名、电子邮件和手机号码
*   提示访问者选择一项服务
*   提示访问者选择日期和时间段
*   显示预订信息

# 建立聊天机器人对话框

现在打开 routes/botman.php 并更改它，以便当有人说“嗨”或“你好”时，BotManController 中的 startConversation 方法将被调用。

```
<?php

use App\Http\Controllers\BotManController;

$botman = resolve('botman');

$botman->hears('._(Hi|Hello)._', BotManController::class.'@startConversation'); 
```

Enter fullscreen mode Exit fullscreen mode

在 boardingConversation 上创建一个新的对话类

```
php artisan botman:make:conversation OnboardingConversation 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将在 app/Http/Conversations/
创建 OnboardingConversation 类

```
<?php

namespace App\Http\Conversations;

use BotMan\BotMan\Messages\Conversations\Conversation;

class OnboardingConversation extends Conversation
{
    public function run()
    {
        //
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看位于 app/Http/Controllers/的 BotManController 类，并更新代码，以便它可以使用我们新的 OnboardingConversation 类。

```
<?php

namespace App\Http\Controllers;

use BotMan\BotMan\BotMan;
use Illuminate\Http\Request;
use App\Http\Conversations\OnboardingConversation;

class BotManController extends Controller
{
    public function handle()
    {
        $botman = app('botman');

        $botman->listen();
    }

    public function tinker()
    {
        return view('tinker');
    }

    public function startConversation(BotMan $bot)
    {
        $bot->startConversation(new OnboardingConversation());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在打开 conversation 类 OnboardingConversation。它扩展了抽象类对话。run 方法是对话的起点，get 会立即执行。

```
<?php

namespace App\Http\Conversations;

use BotMan\BotMan\Messages\Conversations\Conversation;

class OnboardingConversation extends Conversation
{
    public function run()
    {
        //
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 询问姓名、邮箱和手机

让我们请来访者输入他/她的名字。这是一件非常简单的事情。

```
<?php

namespace App\Http\Conversations;

use BotMan\BotMan\Messages\Incoming\Answer;
use BotMan\BotMan\Messages\Conversations\Conversation;

class OnboardingConversation extends Conversation
{
    public function askName()
    {
        $this->ask('What is your name?', function(Answer $answer) {
            $this->say('Nice to meet you '. $answer->getText());
        });
    }

    public function run()
    {
        $this->askName();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它是如何工作的？

当对话开始时，run 方法将调用 askName 方法。在 askName 中，它使用“ask”方法，该方法将在聊天机器人中输出一个问题。可以使用$answer->getText()获取用户响应。

现在机器人想问第二个和第三个问题。所以让我们创建两个方法——ask email()和 askMobile()。

```
public function askEmail()
{
    $this->ask('What is your email?', function(Answer $answer) {

        $validator = Validator::make(['email' => $answer->getText()], [
            'email' => 'email',
        ]);

        if ($validator->fails()) {
            return $this->repeat('That doesn\'t look like a valid email. Please enter a valid email.');
        }

        $this->bot->userStorage()->save([
            'email' => $answer->getText(),
        ]);

        $this->askMobile();
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

有一些有趣的事情需要注意。

首先，我使用 Laravel 验证来验证电子邮件。如果验证失败，聊天机器人会提示用户输入有效的电子邮件地址。“重复”方法将再次询问最后一个问题。

```
$this->repeat('That doesn\'t look like a valid email. Please enter a valid email.'); 
```

Enter fullscreen mode Exit fullscreen mode

userStorage 方法顾名思义就是保存当前聊天机器人的用户信息。因此，我们将使用 userStorage 方法来存储预订信息。

```
$this->bot->userStorage()->save([
    'name' => 'Your name here',
]); 
```

Enter fullscreen mode Exit fullscreen mode

机器人会一个接一个地问问题。这是完整的代码。

```
<?php

namespace App\Http\Conversations;

use Validator;
use BotMan\BotMan\Messages\Incoming\Answer;
use BotMan\BotMan\Messages\Outgoing\Question;
use BotMan\BotMan\Messages\Outgoing\Actions\Button;
use BotMan\BotMan\Messages\Conversations\Conversation;

class OnboardingConversation extends Conversation
{
    public function askName()
    {
        $this->ask('What is your name?', function(Answer $answer) {
            $this->bot->userStorage()->save([
                'name' => $answer->getText(),
            ]);

            $this->say('Nice to meet you '. $answer->getText());
            $this->askEmail();
        });
    }

    public function askEmail()
    {
        $this->ask('What is your email?', function(Answer $answer) {

            $validator = Validator::make(['email' => $answer->getText()], [
                'email' => 'email',
            ]);

            if ($validator->fails()) {
                return $this->repeat('That doesn\'t look like a valid email. Please enter a valid email.');
            }

            $this->bot->userStorage()->save([
                'email' => $answer->getText(),
            ]);

            $this->askMobile();
        });
    }

    public function askMobile()
    {
        $this->ask('Great. What is your mobile?', function(Answer $answer) {
            $this->bot->userStorage()->save([
                'mobile' => $answer->getText(),
            ]);

            $this->say('Great!');
        });
    }

    public function run()
    {
        $this->askName();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们试试我们的聊天机器人，看看是否可行。进入 [http://127.0.0.1:8000](http://127.0.0.1:8000) ，刷新页面，发送‘Hello’开始对话。

[![Botman Booking Chatbot](img/c85fc1375c3bbdf010d0ffc434e0de3c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pze0-6qq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/j0zC79/jhxwnrhxl8lqdsbhypue.png)

## 提示访客选择其中一项服务

现在让我们进入下一个问题。这一次聊天机器人将要求用户选择一项服务。假设沙龙提供 3 项服务——美发、水疗、美容。

创建一个新的会话类 SelectServiceConversation

```
php artisan botman:make:conversation SelectServiceConversation 
```

Enter fullscreen mode Exit fullscreen mode

当我们建立越来越多的对话时，我们希望将对话彼此联系起来。您可以在任何 Conversation 类中使用$ this-> bot-> start conversation(new another conversation())方法将其与另一个会话连接起来。

打开 OnboardingConversation 对话类，并进行以下更改。

```
public function askMobile()
{
    $this->ask('Great. What is your mobile?', function(Answer $answer) {
        $this->bot->userStorage()->save([
            'mobile' => $answer->getText(),
        ]);

        $this->say('Great!');

        $this->bot->startConversation(new SelectServiceConversation()); // Trigger the next conversation
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

BotMan 提供了很酷的方法来询问多项选择问题——问题和按钮类。

```
<?php

namespace App\Http\Conversations;

use BotMan\BotMan\Messages\Incoming\Answer;
use BotMan\BotMan\Messages\Outgoing\Question;
use BotMan\BotMan\Messages\Outgoing\Actions\Button;
use BotMan\BotMan\Messages\Conversations\Conversation;

class SelectServiceConversation extends Conversation
{
    public function askService()
    {
        $question = Question::create('What kind of Service you are looking for?')
            ->callbackId('select_service')
            ->addButtons([
                Button::create('Hair')->value('Hair'),
                Button::create('Spa')->value('Spa'),
                Button::create('Beauty')->value('Beauty'),
            ]);

        $this->ask($question, function(Answer $answer) {
            if ($answer->isInteractiveMessageReply()) {
                $this->bot->userStorage()->save([
                    'service' => $answer->getValue(),
                ]);
            }
        });
    }

    public function run()
    {
        $this->askService();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

isInteractiveMessageReply 方法检测用户是否与消息进行了交互并单击了按钮或只是输入了文本。

让我们试试我们的聊天机器人，看看是否可行。进入 [http://127.0.0.1:8000](http://127.0.0.1:8000) ，刷新页面，发送‘Hello’开始对话。

[![](img/8730d95266650e12f329aa4aef8421b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3_O448VN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/mauen9/dfuktquf760gkx2phnhx.png)

## 提示访客选择日期和时间段

太好了！。正如我们之前所做的，让我们再创建一个对话类，要求用户选择预订的日期和时间段。

```
php artisan botman:make:conversation DateTimeConversation 
```

Enter fullscreen mode Exit fullscreen mode

下面的对话类将首先提示用户选择一个日期，然后选择一个时间段，并将响应保存在当前聊天机器人用户存储中。

这是完整的代码。

```
<?php

namespace App\Http\Conversations;

use Carbon\Carbon;
use BotMan\BotMan\Messages\Incoming\Answer;
use BotMan\BotMan\Messages\Outgoing\Question;
use BotMan\BotMan\Messages\Outgoing\Actions\Button;
use BotMan\BotMan\Messages\Conversations\Conversation;

class DateTimeConversation extends Conversation
{
    public function askDate()
    {
        $availableDates = [
            Carbon::today()->addDays(1),
            Carbon::today()->addDays(2),
            Carbon::today()->addDays(3)         ];

        $question = Question::create('Select the date')
            ->callbackId('select_date')
            ->addButtons([
                Button::create($availableDates[0]->format('M d'))->value($availableDates[0]->format('Y-m-d')),
                Button::create($availableDates[1]->format('M d'))->value($availableDates[1]->format('Y-m-d')),
                Button::create($availableDates[2]->format('M d'))->value($availableDates[2]->format('Y-m-d')),
            ]);

        $this->ask($question, function(Answer $answer) {
            if ($answer->isInteractiveMessageReply()) {
                $this->bot->userStorage()->save([
                    'date' => $answer->getValue(),
                ]);

                $this->askTime();
            }
        });
    }

    public function askTime()
    {
        $question = Question::create('Select a time slot')
            ->callbackId('select_time')
            ->addButtons([
                Button::create('9 AM')->value('9 AM'),
                Button::create('1 PM')->value('1 PM'),
                Button::create('3 PM')->value('3 PM'),
            ]);

        $this->ask($question, function(Answer $answer) {
            if ($answer->isInteractiveMessageReply()) {
                $this->bot->userStorage()->save([
                    'timeSlot' => $answer->getValue(),
                ]);
            }
        });
    }

    public function run()
    {
        $this->askDate();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记在 askService 方法中用 SelectServiceConversation 连接 DateTimeConversation 会话。

```
$this->bot->startConversation(new DateTimeConversation()); 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们使用一个新的对话类 BookingConversation 向用户显示预订的详细信息。

```
php artisan botman:make:conversation BookingConversation 
```

Enter fullscreen mode Exit fullscreen mode

这里是 BookingConversation 类

```
<?php

namespace App\Http\Conversations;

use BotMan\BotMan\Messages\Incoming\Answer;
use BotMan\BotMan\Messages\Outgoing\Question;
use BotMan\BotMan\Messages\Outgoing\Actions\Button;
use BotMan\BotMan\Messages\Conversations\Conversation;

class BookingConversation extends Conversation
{
    public function confirmBooking()
    {
        $user = $this->bot->userStorage()->find();

        $message = '-------------------------------------- <br>';
        $message .= 'Name : ' . $user->get('name') . '<br>';
        $message .= 'Email : ' . $user->get('email') . '<br>';
        $message .= 'Mobile : ' . $user->get('mobile') . '<br>';
        $message .= 'Service : ' . $user->get('service') . '<br>';
        $message .= 'Date : ' . $user->get('date') . '<br>';
        $message .= 'Time : ' . $user->get('timeSlot') . '<br>';
        $message .= '---------------------------------------';

        $this->say('Great. Your booking has been confirmed. Here is your booking details. <br><br>' . $message);
    }

    public function run()
    {
        $this->confirmBooking();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/9e3d49ca13369cbc315655545b69cf49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jlXCJ-M---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/nM6TEp/Bot_Man_Studio.png)

# 结论

本教程的源代码可以在 Github 上找到。

我希望你觉得这篇文章很有见地。

**我们来连线**🌎

*   我的博客[kirandev.com](https://www.kirandev.com/)
*   在推特上关注我
*   在 [Github](https://github.com/devkiran) 上找到我