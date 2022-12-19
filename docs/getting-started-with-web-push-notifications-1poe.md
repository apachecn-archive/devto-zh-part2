# Web 推送通知入门

> 原文：<https://dev.to/orkon/getting-started-with-web-push-notifications-1poe>

我多次听说过 Web 推送通知和服务人员，但我从未实施过，直到上周 [Just Comments](https://just-comments.com) 。所以我决定分享我关于这项技术的笔记。

首先，有大量关于 Web 推送通知的文章和有用的文档，所以我不会添加新的内容。我将尝试总结一下我认为快速入门和实现一个基本版本的 Web 推送通知需要理解和了解的内容。

让我们从一些关键事实开始:

### #1 推送通知依赖于多个独立的浏览器 API

推送通知的整个功能包括可以单独使用的推送 API 和通知 API。此外，它依赖于服务人员，因为只有他们可以在后台运行，以便能够接收推送通知，即使用户关闭了发送网站。

### #2 并非所有的浏览器都支持推送通知(还没有)

目前，IE && Opera Mini [不支持服务人员](https://caniuse.com/#feat=serviceworkers)和[推送 API](https://caniuse.com/#feat=push-api) ，此外，iOS Safari、安卓版 Chrome、三星浏览器和安卓版 UC 浏览器不支持[网络通知 API](https://caniuse.com/#feat=notifications) 。

这意味着在实现推送通知时，您需要检测浏览器是否支持所需的 API。

## 大图这是怎么回事

我创建了一个简单的图表来概括不同的 API 和概念。它通过一系列动作/它们之间的关系来连接各个部分。

[![](../Images/a2411d09c801d7b0ac6b5f8b62e81d1e.png "Web Push API Workflow")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jpx-k254--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://60devs.com/img/article-getting-started-with-web-push-api/push.png)

所以一切都从您的网页上的一些 JavaScript 代码开始，这些代码注册(1)一个服务人员。服务工作器提供(2)一个服务工作器注册对象，该对象反过来提供对推送管理器实例的访问(3)。如果用户允许，推送管理器可以向我们提供(4)推送订阅。这个订阅对象可以发送到您的后端(5)。在服务器上，您可以使用订阅向用户的浏览器发送消息(6)。服务人员收到(7)该消息，但它不会自动显示给用户。服务人员解析消息，并决定如何处理它。通常，工作人员使用通知 API 显示通知(8)。

## 编码部分

正如我在关键事实中提到的，不是所有的浏览器都支持 API，所以我们需要检查用户的浏览器是否支持服务人员:

```
function supportsPushNotifications() {
  return 'serviceWorker' in navigator && 'PushManager' in window;
}

supportsPushNotifications() === true // if the browser supports needed APIs 
```

Enter fullscreen mode Exit fullscreen mode

在我们确认可以使用服务人员后，我们可以从第一步开始，注册一个服务人员:

```
navigator
  .serviceWorker
  .register('/sw.js')
  .then(swRegistration => {
    // TODO step 2 & 3 here
  })
  .catch(err => {
    console.log('serviceWorker.register failed', err);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码从你网站的根目录获取`sw.js`。`register`函数返回一个承诺。因此，对于成功的案例，我们用`.then`来处理，如果出现问题，我们用`.catch`来处理。

现在，我们可以实现步骤 2 和 3，这需要`swRegistration` :

```
const applicationServerKey = '';
swRegistration
  .pushManager
  .getSubscription()
  .then(subscription => {
    const isSubscribed = !(subscription === null);
    if (!isSubscribed) {
      return swRegistration.pushManager
        .subscribe({
          userVisibleOnly: true,
          applicationServerKey,
        })
        .then(sendSubscriptionToServer);
    }
    sendSubscriptionToServer(subscription);
  })
  .catch(err => {
    console.log('getSubscription failed', err);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在不要担心`applicationServerKey`。`applicationServerKey`允许将订阅与您的服务器相关联。稍后我将解释如何获得这个密钥。

这里发生了什么:我们调用`pushManager.getSubscription`方法，如果用户已经允许推送通知，则返回订阅，否则返回`null`。如果我们已经有一个订阅，我们可以把它发送到后端。如果没有，我们调用`pushManager.subscribe`请求用户允许推送通知。

现在，对于步骤 5，您可以使用任何您喜欢的方法将订阅对象发送到您的服务器。我建议先用`JSON.stringify(subscription)`把它字符串化。

为了从您的服务器向客户端发送消息，我推荐使用`web-push`模块:

```
const webpush = require('web-push');

const vapidKeys = {
  publicKey: '',
  privateKey: '',
};

webpush.setVapidDetails(
  'mailto:your@email',
  vapidKeys.publicKey,
  vapidKeys.privateKey
);

webpush.sendNotification(
  JSON.parse(subscription),
  JSON.stringify({
    title: 'Title',
    icon: 'https://your-site.com/assets/push-icon.png',
    body: 'Body',
    url: 'https://your-site.com/url-to-open',
  })
) 
```

Enter fullscreen mode Exit fullscreen mode

现在在步骤 7 和 8 中，我们返回到`sw.js`，负责接收和显示推送消息的服务工作者代码:

```
self.addEventListener('push', function(event) {
  const message = JSON.parse(event.data.text());
  const title = message.title;
  const url = message.url;
  const options = {
    body: message.body,
    icon: message.icon,
    badge: message.badge,
    data: url,
  };
  event.waitUntil(self.registration.showNotification(title, options));
});

self.addEventListener('notificationclick', function(event) {
  event.notification.close();
  event.waitUntil(clients.openWindow(event.notification.data));
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里定义了两个事件侦听器。使用`push`监听器，我们解析消息并调用`showNotification`方法来显示通知。在`notificationclick`上，我们关闭通知并导航到通过推送消息发送的 URL。

## 生成密钥

您可以使用`web-push`库来生成密钥。密钥生成需要一次完成。前端和后端都使用相同的公钥，后端只使用私钥:

```
const webpush = require('web-push');
const vapidKeys = webpush.generateVAPIDKeys();
console.log(vapidKeys); 
```

Enter fullscreen mode Exit fullscreen mode

您应该在需要的地方指定前面代码片段中的键。

## 结论

我发现推送通知的 API 非常简单明了。然而，在开始的时候，要花相当多的时间让你的头脑围绕所有的概念和活动部分。

我希望这些注释对您有用，并且在您需要实现推送通知时会用到它们。如果这个时候来了，请不要在网页加载时就要求许可:这很烦人，大多数人会阻止它。

感谢阅读！