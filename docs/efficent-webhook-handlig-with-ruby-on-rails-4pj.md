# 使用 ruby on rails 实现高效的 webhook 处理

> 原文：<https://dev.to/arandilopez/efficent-webhook-handlig-with-ruby-on-rails-4pj>

*原载于[Dev Knights](https://dev-knights.github.io/2018/06/03/efficient-webhook-handling-with-ruby-on-rails.html?utm_source=devto&utm_medium=blogspot)T3】*

当实现 Paypal 或 Stripe 等支付系统时，更繁琐的部分是实现 webhooks。在这篇文章中，我将向你解释如何优化 webhook 并得到一个简洁的实现。

* * *

## web hooks 是干什么用的？

Webhooks 是应用程序中的端点，Paypal 或 Stripe 之类的服务会通知你应用程序中发生的事件。例如，Paypal 和 Stripe 用它们来通知收费是否成功。鉴于充电过程是不同步的，这种机制是必要的。因此，在我们使用他们的 API 创建收费的时候，我们不会知道收费是否成功，直到将来。

## 标准方式

webhook 只是一个通过`POST`方法接收信息的端点(路由或路径)。在 Rails 中，Stripe webhook 的准系统实现可以是:

```
# controllers/stripe_controller.rb
# Method responsbile for handling stripe webhooks
def webhook
  begin
    event = JSON.parse(request.body.read)
    case event['type']
      when 'invoice.payment_succeeded'
        # handle success invoice event
      when 'invoice.payment_failed'
        # handle failure invoice event
      # More events...
    end
  rescue Exception => ex
    render :json => {:status => 400, :error => "Webhook failed"} and return
  end
  render :json => {:status => 200}
end 
```

Enter fullscreen mode Exit fullscreen mode

这个`webhook`方法使用一个`switch-case`来根据接收到的事件类型确定执行哪个代码块。现在想象一下实现更多事件，这个方法将变得难以维护。

当然，我们可以编写更多的方法来放置每个事件的逻辑，并将它们调用到`switch-case`中，但是我们仍然可以改进这个 webhook 方法。

## 清洁工道

我见过的最干净的 webhook 实现是在[的 Laravel 收银台](https://laravel.com/docs/5.6/billing#defining-webhook-event-handlers)。收银员的 Webhook 控制器允许您通过将每个事件映射到一个方法来自动处理。控制器约定说，如果你想处理事件`invoice.payment_succeeded`，在控制器中创建一个方法，其名称以`handle`开头，事件类型的`camelCase`形式:`handleInvoicePaymentSucceeded`。

```
<?php

namespace App\Http\Controllers;

use Laravel\Cashier\Http\Controllers\WebhookController as CashierController;

class WebhookController extends CashierController
{
    /**
     * Handle a Stripe webhook.
     *
     * @param  array  $payload
     * @return Response
     */
    public function handleInvoicePaymentSucceeded($payload)
    {
        // Handle The Event
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重构

我们如何用 Rails 实现这一点呢？。很容易，这要感谢 Ruby 中的元编程。重构前面的方法可能会变成这样:

```
def webhook
  begin
    event = JSON.parse(request.body.read)
    method = "handle_" + event['type'].tr('.', '_')
    self.send method, event
  rescue JSON::ParserError => e
    render json: {:status => 400, :error => "Invalid payload"} and return
  rescue NoMethodError => e
    # missing event handler
  end
  render json: {:status => 200}
end

def handle_invoice_payment_succeeded(event)
  #handle the event
end

def handle_invoice_payment_failed(event)
  #handle the event
end

# and more... 
```

Enter fullscreen mode Exit fullscreen mode

在 webhook 方法中，我们需要将事件类型解析为处理该事件的方法的名称。

1.  将所有的`'.'`翻译成`'_'`，并与`"handle_"`连接。
2.  然后发送消息，用获得的有效负载调用结果方法。
3.  否则，如果没有为该事件实现的方法，它将被拯救，我们可以在那里进行另一个操作。

Laravel Cashier 实现处理 webhook 事件看起来棒极了。因此，这样你就得到一个干净的、可维护的控制器。

### ProTip

下一步，您可以将[关注点](http://api.rubyonrails.org/classes/ActiveSupport/Concern.html)写入组事件处理程序。现在你有一个更轻的控制器。

你想用西班牙语读这篇文章吗？检查一下[这里](https://dev-knights.github.io/spanish/2018/06/03/manejo-eficiente-de-webhooks-con-ruby-on-rails.html?utm_source=devto&utm_medium=blogspot)T3】