# Laravel 5.6 中的通知特定路由

> 原文：<https://dev.to/freekmurze/notification-specific-routing-in-laravel-5-6-3o6m>

Laravel 有一个非常强大的通知系统。从文档中:

> 除了支持发送电子邮件，Laravel 还支持通过各种交付渠道发送通知，包括邮件、SMS(通过 Nexmo)和 Slack。通知也可以存储在数据库中，以便显示在您的 web 界面上。

总结你可以通过某个渠道发送一个`Notification`给一个`Notifiable`。可通知的可以是任何使用`Notifiable`特征的类，例如用户。信道的一个很好的例子是松弛。

在 Laravel 5.4 和 5.5 中，这是您如何指定在向用户发送通知时使用的 slack webhook。

```
class User extends Authenticatable
{
    use Notifiable;

    /**
     * Route notifications for the Slack channel.
     *
     * @return string
     */
    public function routeNotificationForSlack()
    {
        return $this->slack_webhook_url;
    }
} 
```

问题是没有办法向特定的 Slack webhook 发送特定的通知。

在 Laravel 5.6 中，这个[是通过使用通知来解决的](https://github.com/laravel/framework/pull/22289)，该通知被传递给 notifiable 上的路由方法。

```
class User extends Authenticatable
{
    use Notifiable;

    /**
     * Route notifications for the Slack channel.
     *
     * @return string
     */
     public function routeNotificationForSlack($notification)
     {
          if ($notification instanceof MySpecialSnowflakeNotification) {
               return $this->alternative_slack_webhook;
          }

          return $this->slack_webhook;
     }
} 
```

如果你想知道更多 Laravel 5.6 的新特性，请务必在本周三观看 [Laracon Online](https://laracon.net/) 的 [Taylor 的演讲](https://twitter.com/taylorotwell/status/958725579066499072)。