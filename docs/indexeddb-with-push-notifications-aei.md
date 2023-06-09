# 带推送通知的索引数据库

> 原文：<https://dev.to/adrienshen/indexeddb-with-push-notifications-aei>

## 渐进式网络应用简介

自从杰克·阿奇博尔德(Jake Archibald)在 [Google I/O 2016 事件](https://www.youtube.com/watch?v=cmGr0RszHc8)中透露了进步的 web 应用和服务工作者可以做什么的一瞥之后，我一直对这一套新的 web 技术在开发下一代移动 web 应用中可以解决的潜在应用相当乐观。PWAs 允许开放网络的可发现性与本地应用的参与性和响应性相结合。真的是两全其美。pwa 通过服务工人严格缓存网站资产和脚本，使用服务工人进行推送通知和后台同步，以及“添加到主屏幕”等功能来给本机应用程序带来喜欢的感觉。这只是对渐进式网络应用的简单介绍。要了解更多，只需浏览完整的 Google PWA 教程和各种 Google I/O 讲座。在这篇文章中，我想展示的是在收到 web 推送通知时访问 IndexedDB 的用法，以及如何使用它来实现一些非常简洁的功能。

## 在网络上推送通知

服务工作者使之成为可能的核心特征之一是允许网络浏览器接收网络推送通知，这曾经是本地移动应用的专有领域。这个功能[现在除了 Safari](https://caniuse.com/#search=push%20api) 之外，每个主流的桌面和移动浏览器都实现了。例如，企业可以使用这些 web 推送通知来通知客户向收银员使用的商家渐进式 web 应用进行了支付。有一个服务器端实现需要将 push 发送到指定的 push 服务，但是您可以使用 curl 或 Postmen 来模拟。服务工作器文件中的客户端应用程序推送监听器代码将如下所示:

```
// most basic push notification example
self.addEventListener('push', function(event) {
    if (event.data) {
        const pushTitle = "A simple push notification";
        const pushOptions = {
            body: "push content body",
            icon: img/logo-192x192.png",
            vibrate: [100,50,100],
        };

        self.registration.showNotification(
            pushTitle,
            pushOptions,
        )
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

当设备接收到来自浏览器特定推送服务的推送时，服务工作者将暂时醒来并执行处理器内的代码，并向用户显示适当的推送消息。在推送中可以配置许多设置，例如优先级、徽章、标签和事件声音。更全面的解释请看这里:[https://web-push-book.gauntface.com/](https://web-push-book.gauntface.com/)

## 服务人员范围

服务工作者是一个了不起的工具，是它让现代一代的原生 web 应用成为可能。有很多关于服务工作者的介绍，比如这里的这个，但是这里要认识到的一个重要概念是，服务工作者并不像我们常规的 Javascript 代码一样运行在同一个环境中。这意味着服务人员不能访问像 DOM 这样的普通浏览器 API，也不能访问像地理定位或本地存储这样的 API。这在一开始对我来说并不明显，并且会对我们需要实现的特性造成一些挫折。不是直接访问页面 DOM 元素，而是服务人员需要通过应用程序将监听的 API(如`PostMessage`)发送消息，并使用处理程序进行响应。但是，如果我们在推送通知事件时需要像用户地理定位这样的功能呢？这就是 IndexDB 的用武之地。

## 为什么要用 IndexedDB？

IndexedDB 是推荐用于大量数据和更多结构化存储的 web 浏览器数据对象存储。浏览器上还有其他持久存储，如 LocalStorage/SessionStorage、WebSQL 和 AppCache。LocalStorage 有一定的限制，例如大多数浏览器的最大存储空间为 5mb 到 10mb，所有数据都保存在字符串中。我只在简单的情况下使用它，比如记住用户是否已经看过教程幻灯片。不再支持 WebSQL 和旧的应用程序缓存，因此可能不建议在 2018 年开发离线应用程序时使用。

IndexedDB 具有:
-面向开发人员的低层灵活接口
-主要是异步的
-事务，所有数据库操作必须是事务的一部分
-最重要的是，它可以通过服务人员访问，这是我们在这里的主要用途

### 在 IndexedDB 中存储地理位置

我们知道服务人员不允许直接访问`Geolocation` API，也不能访问`LocalStorage`。因此，让我们将地理位置数据存储在 IndexDB 中。我们将使用杰克的 [idb 助手库](https://github.com/jakearchibald/idb)，这样我们就可以利用 Promises，它提供了一个更干净的接口。

```
// idb.js
import idb from 'idb';

export const idbPromise = idb.open('realtime-app', 2, db => {
    switch (db.oldVersion) {
        case 0:
        // placeholder so that block executes if there's no db
        case 1:
            console.log("creating object store: deviceGeolocation");
            db.createObjectStore("deviceGeolocation", {autoIncrement: true});
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一种方法来保持数据库更新的完整性，因此我们导出一个助手`idbPromise`,它将为拥有不同数据库版本的用户处理数据库更新。接下来，我们使用地理定位 API 来查询用户设备的位置。

```
import { idbPromise } from "idb.js"

export const calculateGeoLocation = () => {
    if (navigator.geolocation) {
        navigator.geolocation.watchPosition(position => {
            _storeGeoIndexedDB(position.coords);
        })
    }
}

const _storeGeoIndexedDB = ({Lat, Lon}) => {
    if (!window.indexedDB) return;
    const Timestamp = new Date();

    idbPromise.then(db => {
        let tx = db.transaction('deviceGeolocation', 'readwrite');
        let deviceGeolocation = tx.objectStore('deviceGeolocation');
        let entry = { Lat, Lon, Timestamp };
        deviceGeolocation.add(entry);

        return tx.complete;
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

我们创建一个函数来查询地理位置，并使用我们之前创建的助手 idb Promise 将其作为对象存储在我们的 **deviceGeolocation** 数据库中。现在我们可以在应用程序中任何我们认为合适的地方使用`calculateGeoLocation`。我们甚至可以每隔一段时间运行一次，或者作为对用户操作的响应。在我的情况下，我通常会要求地理定位，作为与用户驱动的操作相关的应用程序启动过程的一部分。

这是它在 Chrome **应用标签**
[![IndexDB table geolocations database, not creepy at all](img/911423e4745892d30d88bacd26f7e8c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yi4mZopt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tvdwjm3l4f40huf0az8u.png) 中的样子

### 地理定位 API 注释

有两个主要的本地 web 地理位置 API 可供开发人员使用。`Geolocation.getCurrentLocation()`和`Geolocation.watchPosition()`。我们在这里使用`Geolocation.watchPosition`,因为后者对于移动设备来说应该有更好的准确性，并且当设备位置改变时会更新位置。这两种方法都允许传入一个`PositionOptions`参数来进一步定制您的特定需求。

为了获得更好的精度，请将`PositionOptions.enabledHighAccuracy`设置为`true`，但这可能会导致响应时间变慢，并更快地耗尽设备电池。

另一个有用的选项是`PositionOptions.maximumAge`,这是允许设备返回缓存地理位置的时间量(毫秒)。同样，这取决于具体的使用情况，如果用户位置不必是最新的或准确的，则可以节省一些计算。

## 做作用例示例

现在，我们希望将服务工作者、推送通知 API、IndexedDB 和用户地理位置结合在一起，为我们的移动应用程序构建一些有用的功能。想象一下，我们有一个完全虚构的移动应用程序，它在我们的云服务器上预测火山爆发，并在可能爆发时向用户设备发送推送通知。来自后端的推送负载将如下所示。

```
{"data" : {
    "Lat" : 2.18,
    "Lon": 102.89,
    "Vei" : 6.8,
    "EruptionStartTime" : "2018-06-20 10:55:00.764269",
    "Region": ""},
    "type" : "SERIOUS"
} 
```

Enter fullscreen mode Exit fullscreen mode

纬度和经度是火山爆发的坐标。喷发起始时间是我们的服务器预测的未来喷发时间。VEI 是这次喷发的计算强度。

假设需求是让渐进式 web 应用程序在后台接收推送，然后根据用户的当前位置决定相对影响。换句话说，他是应该逃命，还是应该冷静下来。我们需要做的一件事是计算从爆发点和用户当前位置的距离。我们可能还想计算他们在热浪和有毒气体到达之前还有多长时间。然后，我们希望显示通知，显示所有超级有用的数据。

## 在推送事件中访问 IndexedDB

让我们回到推送监听器事件。我们需要访问 IndexedDB 中的用户存储位置，进行所有的计算，然后在推送事件中向用户显示相关的推送消息。

```
// contrive use case example
self.addEventListener('push', function(event) {
    if (event.data) {
        const pushTitle = "Volcanic Alert";
        const pushOptions;
        const pushEventData = event.data.json();
        const eruptionPayload = JSON.parse(pushEventData.data);

        if (pushEventData.type === "SERIOUS") {
            // Access IndexDB for our geolocation data using the same
            // idb helper from earlier
            let notifResults = idbPromise().then(db => {

                let tx.db.transaction("userGeolocation", "readonly");
                let userGeolocation = tx.objectStore("userGeolocation");
                return userGeolocation.getAll();
            }).then(userGeolocationRecords => {

                const mostRecentGeolocation =
                    userGeolocationRecords[userGeolocationRecords.length - 1];

                // Calculate spherical distance from eruption
                const deviceSphericalDistanceFromEruption =
                    getSphericalDistance(
                        mostRecentGeolocation["Lat"],
                        mostRecentGeolocation["Lon"],
                        eruptionPayload["Lat"],
                        eruptionPayload["Lon"],
                    );

                // Calculate relative intensity
                const intensityReadableString = calculateIntensity(
                    deviceSphericalDistanceFromEruption,
                    eruptionPayload['Vei'],
                )

                // We might also want the relative time for impact to arrive
                const timeTillSeconds = getTimeTill(deviceSphericalDistanceFromEruption, eruptionPayload["EruptionStartTime"]);

                payload.intensityHumanReadable = intensityHumanReadable;
                payload.timeTillSeconds = timeTillSeconds;

                // Now return the calculate data from the promise
                return {
                    body: "Intensity: " + intensityReadableString,
                    tag: "alert",
                    data: payload,
                    renotify: true,
                    requireInteraction: true, // it's pretty important for him to see this
                }
            });

            // Now finish with event.waitUntil()
            event.waitUntil(
                ...
            )
        }

        // Handle other types here...
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 在 event.waitUntil()中显示通知

我们知道对 IndexDB 的查询是异步的，并将返回一个承诺。这个承诺链正是`event.waitUntil`所期待的。`event.waitUntil`将等到承诺得到解决，然后显示通知，然后终止服务工作进程。这是让 indexedDB 和 Web Push 和谐工作的诀窍。**理解这一部分非常重要，可以避免使用带有异步 API 的服务人员带来的大量麻烦**。

```
// Finishing up with notification
  event.waitUntil(
    notifResults.then(notifOptions => {
      isClientFocused().then(clientOpts => {
          clientOpts.windowClients.forEach(windowClient => {
            windowClient.postMessage({
              message: 'Received a volcano alert.',
              time: new Date().toString(),
              intensity: notifOptions.data.intensityReadableString,
              seconds: notifOptions.data.timeTillSeconds,
            });
          });

        self.registration.showNotification(
          pushTitle, // Volcanic Alert
          notifOptions,
        )
      })
    })
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还应该处理这样一种情况，即无论出于什么原因，用户已经在他的设备上打开了应用程序。在这种情况下，我们应该使用类似上面所示的`PostMessage`这样的 API 将来自服务人员的消息发布到客户端应用程序。我在这里使用了一个助手方法`isClientFocused()`,如果可用的话，它将为我提供打开的客户端。如果您不需要处理这种情况，那么您可以忽略这一部分，直接显示通知。

## 进一步考虑

**一直显示通知**。有时你会看到 Chrome 显示 ***“本网站已在后台更新。”*** 浏览器期望显示通知，但在`event.waitUntil()`结束后通知没有显示，或者有东西坏了。只要检查一下`self.registration.showNotification`是否被调用，并且在此之前没有错误发生。

**处理通知点击**。虽然这不是本文的主题，但请务必考虑通知点击事件之后会发生什么。也许它会把他们带到应用程序的网址，显示我们这次火山爆发的倒计时。

服务人员可能相当棘手。在过去的几个月里，我在生产过程中使用服务人员时遇到了一些错误和意外——有时是在流量非常高的网站上。也许有一天我会写下它，但我总是喜欢链接到这个视频:[https://www.youtube.com/watch?v=CPP9ew4Co0M](https://www.youtube.com/watch?v=CPP9ew4Co0M)。正如视频中所述，服务人员可能是一只复杂的野兽，因此使用像`sw-precache`这样的生成器可能非常有帮助。

**确保在本地主机**上测试。服务工作者安装需要 HTTPs 来防止诸如中间人之类的攻击，但是 localhost 将作为该规则的例外。

**与你的核心 app** 沟通。因为服务工作者充当独立于 web 页面的代理服务器，所以我们需要使用特殊的方法在两个独立的上下文之间进行通信。我过去用过的两种方法是`PostMessage()`和在`notificationclick`上通过 URL 传递数据。

希望这篇文章对你有帮助，如果你对类似的实现有任何问题，请告诉我。

### 来源

[https://developer . Mozilla . org/en-US/docs/Web/API/Service _ Worker _ API/Using _ Service _ Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers)
T3】https://developer . Mozilla . org/en-US/docs/Web/API/indexed db _ API/Using _ indexed db
[https://geology.com/stories/13/volcanic-explosivity-index/](https://geology.com/stories/13/volcanic-explosivity-index/)