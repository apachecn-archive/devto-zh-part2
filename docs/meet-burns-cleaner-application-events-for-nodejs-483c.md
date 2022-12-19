# 认识 Burns:node . js 的 cleaner 应用程序事件

> 原文：<https://dev.to/shalvah/meet-burns-cleaner-application-events-for-nodejs-483c>

嗨，戴夫，致社区，

我最近出版了我的 NPM 包的一个新版本， [Burns](https://github.com/shalvah/burns) 。Burns 是一个节点模块，用于编写干净的事件驱动代码并轻松管理您的应用程序事件。它的灵感来自拉勒维尔的[事件](https://laravel.com/docs/master/events)和[广播](https://laravel.com/docs/master/broadcasting)系统。

以下是如何使用它的简要概述:

*   首先，定义一个事件处理程序:

```
// handlers/order.js

function sendOrderShippedEmail(data)
{
    mailer.sendEmail(`Hi ${data.userName}, Your order ${data.orderId} has been shipped`);
} 
```

Enter fullscreen mode Exit fullscreen mode

*   然后将处理程序附加到一个事件:

```
let orderHandlers = require('./handlers/order');
burns.registerEvents({
  orderShipped: orderHandlers.sendOrderShippedEmail
}); 
```

Enter fullscreen mode Exit fullscreen mode

*   然后在您准备好的时候分派事件！

```
burns.dispatch('orderShipped', {
    orderId: order.id, 
    userName: user.name
}); 
```

Enter fullscreen mode Exit fullscreen mode

您还可以通过指定几个选项来广播您的事件(例如，通过 [Pusher](https://pusher.com) )。

请做[查看](https://github.com/shalvah/burns)并分享你的观点。😊

我希望得到一些反馈:

1.  包装的实用性。您认为这个软件包在您的工作中有什么实际应用吗？
2.  *外部 API。*API 是否清晰易用？
3.  *文档。*自述是否清晰简洁？
4.  (奖金:代码质量。有什么我应该注意的错误或事情吗？)

此外，如果你知道有任何工具提供类似的功能，我很乐意听到他们。

谢谢！