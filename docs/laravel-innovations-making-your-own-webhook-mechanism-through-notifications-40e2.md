# Laravel 创新:通过通知创建自己的 webhook 机制

> 原文：<https://dev.to/slyfirefox/laravel-innovations-making-your-own-webhook-mechanism-through-notifications-40e2>

<figure>[![](img/f32a5b090c16fe01c2c21c8f6ee6af96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2sGlj1ZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AHENwWLb9y57Uuh4v.)</figure>

当我们集成第三方服务时，我们经常需要实现 webhooks。Webhooks 是一个 web 应用程序通过 HTTP 请求向其他 web 应用程序发送数据或事件的地方。例如，大多数电子邮件服务，如 mailgun 和 spark post，在发送应用程序电子邮件时，会发送与硬退信或垃圾邮件通知相关的消息。

通过简单地创建一个控制器来接收 webhook 消息是非常容易的，通过 Laravel 令人惊叹的[通知框架](https://laravel.com/docs/5.6/notifications)来发送它们也几乎一样简单。我们将如何做是创建一个[定制通知通道](https://laravel.com/docs/5.6/notifications#custom-channels)。

#### 安装我们的依赖项

首先，我们需要安装一个用于发送 HTTP 请求的库。我们将在这里使用的是 Guzzle，它在大多数 PHP web 应用程序中以这样或那样的方式被广泛使用。

我们可以通过使用 composer 来安装它。

```
composer require guzzlehttp/guzzle:~6.0 
```

Enter fullscreen mode Exit fullscreen mode

这样做之后，我们就可以开始制作新的 Webhook 通知通道了。

#### 通知渠道

Laravel 中的通知通道设置了一种机制，可以向用户、团队以及你在众多“通道”中找到的任何东西发送结构化消息，这些“通道”可以是电子邮件、备用通道或 SMS 消息。这些通道本身经常发送 HTTP 请求，所以这并不是我们在这里做的新东西。

首先，我们将创建一个 app/Channels 文件夹，并在其中创建一个 WebhookChannel 类。该类将实现一个 send 方法，该方法采用一个 Notifiable 对象和一个 Notification 对象。

由于框架使用通道的方式，我们还将在新类中加入一些依赖项，方法是通过一个构造函数将一个 Guzzle 客户端和一个 logger 实例放入我们的通道中。日志记录器主要是为了让我们的生活更轻松，确保我们的消息被发送出去。

因此，此时我们的 WebhookChannel 应该是这样的: