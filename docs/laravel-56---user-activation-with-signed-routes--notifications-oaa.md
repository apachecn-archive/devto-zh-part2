# Laravel 5.6 -用户激活签名路线和通知

> 原文：<https://dev.to/fwartner/laravel-56---user-activation-with-signed-routes--notifications-oaa>

我们已经看到了很多帮助我们在应用中轻松实现用户激活功能的包。

良好的..有一种更简单的方法，只使用 Laravel 内部构件。

## 我们将在本文中介绍的内容:

*   Laravel 通知
*   Laravel 签名的 URL
*   基本中间件使用

## 开始吧！

*注意:*我假设你的机器上已经全局安装了`Laravel/installer`。

首先，我们需要一个干净的应用:
`laravel new my-project-name`

现在让我们在您选择的 IDE 中打开新项目..
一旦你设置好项目，我们就可以直接进入实际的“魔术”..！

但是首先，我们需要向我们的应用程序添加基本的身份验证内容:

`php artisan make:auth`

## 等待..！我们要实现什么？

良好的..这很简单。我们试图提供一种简单的方法，让我们的用户通过使用 Laravel 通知和签名的 URL 来验证他们的帐户

## 这里不再重复我们正在做的事情！

设置您的用户迁移以提供一个布尔值，我们可以通过添加以下行来检查用户是否通过验证:

```
$table->boolean('is_activated')->default(false); 
```

之后，我们的用户迁移应该如下所示:

```
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('email')->unique();
            $table->string('password');
            $table->boolean('is_activated')->default(false);
            $table->rememberToken();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('users');
    }
} 
```

现在我们需要像这样设置我们的用户模型:

```
...
protected $fillable = ['name', 'email', 'password', 'is_activated'];
...
protected $casts = ['is_activated' => 'boolean'];
... 
```

所以用户模型应该是这样的:

```
<?php

namespace App;

use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use Notifiable;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'email', 'password', 'is_activated'
    ];

    protected $casts = [
        'is_activated' => 'boolean'
    ];

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password', 'remember_token',
    ];
} 
```

现在我们可以直接更新`is_activated`字段。

接下来，我们需要一个新的通知:
`php artisan make:notification Users/ActivateEmailNotification`

让我们继续我们的通知..

我们接下来要做的是使用创建的通知类向用户发送一个链接，当他想使用我们的应用程序时，他需要单击该链接(这个链接受到我稍后将向您展示的新特性的保护..).

因此，让我们创建我们的网址:

```
<?php

namespace App\Notifications\Users;

use App\User;
use Illuminate\Support\Facades\URL;
use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;

class ActivateEmailNotification extends Notification
{
    use Queueable;

    /**
     * User Object
     *
     * @var App\User
     */
    public $user;

    /**
     * Create a new notification instance.
     *
     * @return void
     */
    public function __construct(User $user)
    {
        $this->user = $user;
    }

    /**
     * Get the notification's delivery channels.
     *
     * @param  mixed  $notifiable
     * @return array
     */
    public function via($notifiable)
    {
        return ['mail'];
    }

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
    {
        $url = URL::signedRoute('activate-email', ['user' => $this->user->id]);

        return (new MailMessage)
                    ->subject('Activate your email address')
                    ->line('In order to use the application, please verify your email address.')
                    ->action('Activate your account', $url)
                    ->line('Thank you for using our application!');
    }

    /**
     * Get the array representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return array
     */
    public function toArray($notifiable)
    {
        return [
            //
        ];
    }
} 
```

我们使用 [Laravel 签名 url 特性](https://laravel.com/docs/5.6/urls#signed-urls)为用户创建 URL。这有助于我们确保只有电子邮件发送到的用户有能力验证他的电子邮件地址。

我们也可以说，url 只在指定的时间内有效。

为此，我们需要将 url 从

```
$url = URL::signedRoute('activate-email', ['user' => $this->user->id]); 
```

到

```
$url = URL::temporarySignedRoute('activate-email', now()->addMinutes(30), ['user' => $this->user->id]); 
```

现在，url 在创建后仅 30 分钟内有效。

接下来，我们需要修改我们的`Auth\RegisterController`。
只需将`'is_active' => false,`添加到控制器底部的`create`方法中。用同样的方法，我们将通知用户。

控制器将如下所示:

```
<?php

namespace App\Http\Controllers\Auth;

use App\User;
use App\Http\Controllers\Controller;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Validator;
use Illuminate\Foundation\Auth\RegistersUsers;

class RegisterController extends Controller
{
    /*
    |--------------------------------------------------------------------------
    | Register Controller
    |--------------------------------------------------------------------------
    |
    | This controller handles the registration of new users as well as their
    | validation and creation. By default this controller uses a trait to
    | provide this functionality without requiring any additional code.
    |
    */

    use RegistersUsers;

    /**
     * Where to redirect users after registration.
     *
     * @var string
     */
    protected $redirectTo = '/home';

    /**
     * Create a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('guest');
    }

    /**
     * Get a validator for an incoming registration request.
     *
     * @param  array  $data
     * @return \Illuminate\Contracts\Validation\Validator
     */
    protected function validator(array $data)
    {
        return Validator::make($data, [
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:6|confirmed',
        ]);
    }

    /**
     * Create a new user instance after a valid registration.
     *
     * @param  array  $data
     * @return \App\User
     */
    protected function create(array $data)
    {
        $user = User::create([
            'name' => $data['name'],
            'email' => $data['email'],
            'is_activated' => false,
            'password' => Hash::make($data['password']),
        ]);

        $user->notify(new \App\Notifications\Users\ActivateEmailNotification($user));

        return $user;    }
} 
```

我们还需要一个中间件来确定用户是否被激活。

使用命令
创建一个

像这样修改中间件:

```
<?php

namespace App\Http\Middleware;

use Closure;

class CheckUserActivated
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
        if (!$request->user()->is_activated) {
            return redirect('/not-enabled'); // You can add a route of your choice
        }

        return $next($request);
    }
} 
```

现在我们需要向内核注册我们的新中间件。
打开`App\Http\Kernel.php`并将其添加到类底部的`routeMiddleware`:

```
'active' => \App\Http\Middleware\CheckUserActivated::class, 
```

我们需要向该文件添加另一个类:

```
'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class, 
```

现在我们能够确定用户是否是活动的。如果他不活跃，我们希望将他重定向到另一个页面，在那里您可以为用户添加一个注释，即他仍然需要使用我们电子邮件中的链接来激活他的帐户。

在通知中，我们为用户添加了一个 url 来确认他的电子邮件地址。为了使用该链接，我们需要将该路线添加到我们的 web-routes 文件中:

```
Route::get('/activate-email/{user}', function (Request $request) {
    if (!$request->hasValidSignature()) {
        abort(401, 'This link is not valid.');
    }

    $request->user()->update([
        'is_activated' => true
    ]);

    return 'Your account is now activated!';
})->name('activate-email'); 
```

如果愿意，您可以将该方法放入一个单独的控制器中。但我喜欢保持简单。

这条路线将激活用户，并让他知道，他不能使用应用程序。

## 结论

我真的希望你喜欢这篇关于用户激活的简短教程，它直接来自 Laravel 的简单助手。

如果有，请分享这篇文章，传播❤️的爱

当然，一定要在推特上关注我

这篇文章的来源可以在 [GitHub](https://github.com/fwartner/laravel-user-activation) 上找到😺