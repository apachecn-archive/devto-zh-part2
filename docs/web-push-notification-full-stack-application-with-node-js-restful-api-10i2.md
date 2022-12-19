# 带有节点 Js Restful API 的 Web 推送通知全栈应用程序

> 原文：<https://dev.to/saurabharch/web-push-notification-full-stack-application-with-node-js-restful-api-10i2>

我先告诉你，为什么 **Web 推送通知**对于任何 Web 应用都是必不可少的。

> 它有助于向 web 应用程序的订户广播短消息，并有助于支持用户参与您的应用程序以通知每个订户，任何组织都很难发送短消息事件循环的电子邮件通知或广播短消息的提供和更新等消息，这也会增加服务器运营的成本，因此解决方案是 **Web 推送通知**。这有助于你获得最大的后可达性，并在网络应用上获得更多的消费者参与。

让我告诉你它是如何工作的。
[![web-push-how-working.png](../Images/c3cfa443fbdc8d973892d51d0e022c34.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--076-ea75--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/3iE3ihH2TLqI64zeNDsF)

服务工作者是此功能的主要关键组件和骑士，它安装在客户端浏览器中，并在作为服务工作者安装在浏览器中后作为应用程序独立运行，服务工作者定期向提供商服务器发送查询，请求任何新事件发生，然后响应客户端，如果服务器中发生任何新事件，它会弹出类似
[![popup-notify.png](../Images/460f0c91e274a538e0cefb7967b6f247.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VOp4VxXt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/632p2e4RROJCAUPdGPKg) 的消息

点击“允许”按钮，服务人员开始在客户端浏览器中进行安装，并向具有**公共 VAPID 密钥**的订户发送承诺请求，并在服务器上检查用户是否已经订阅通知，如果已经订阅，则返回错误声明的请求，否则服务器发送真实请求。
大功告成。
现在让我们来看看编码部分，以及如何在您的应用程序中实现这一功能，而无需使用第三方付费服务，只要您使用。

## **第一步**

如果您的系统中没有，需要下面列出的 Prequest。
**先决条件:**

1.  IDE [Visual Studio 代码](https://visualstudio.microsoft.com/)
2.  [节点 j](https://nodejs.org/en/download/)
3.  [去](https://git-scm.com/)
4.  [MongoDB](https://www.mongodb.com/download-center?jmp=nav#charts)
5.  [邮递员](https://www.getpostman.com/apps)

现在让我们开始下一步

## **第二步**

打开你的 IDE [Visual Studio 代码](https://visualstudio.microsoft.com/)
然后用你的 IDE 在集成终端中运行命令

```
git init 
```

然后添加所有字段，或者根据需要跳过
，然后再次运行命令，安装所有的依赖项

```
npm install express web-push body-parser mongoose q --save 
```

[![git-init-commands.png](../Images/9a7d89705f6926736df4b30cc1205070.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uj4E3tyh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/GPPnJskQTGhfljoNHSEn)

```
touch server.js 
```

[![add-new-file-app.png](../Images/803c41ddba3afecef6e550436bdb1e2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--btxUqdKv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/Vp8V4BfDQV67JLwYgoCD) 
再次需要通过如下命令在同一个项目目录下创建三个文件夹

```
mkdir config
cd config 
touch keys.js
touch keys_prod.js
touch keys_dev.js 
```

[![creat-config-dir.png](../Images/3121ff8d7e1e65f70481edb8f87f95ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sn8bNkLj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/fgYHnCfRAWCT59r8yR2f)

```
mkdir model
cd model
touch subscribers_model.js 
```

[![creat-model-dir.png](../Images/afefd1dba640ff6b485a2b20a8342ed4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--STtqUdui--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/zcdVJawpQ1udam8tvJE7)

```
mkdir router
cd router
touch push.js
touch subscriber.js
touch index.js 
```

[![creat-router-dir.png](../Images/f29df5bcc81fcd284e1146fe79f50fb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5rikMKYP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/Za6RRQStQMqtqALHMkto)

现在所有必要的文件夹和文件都已创建，在这个项目中，我们将在下一步进入下一个编码部分。

## **第三步**
这个项目的文件结构如下

```
|
|
|________________________./config
|                         |
|                         |____keys_prod.js
|                         |____keys_dev.js
|                         |____keys.js
|
|________________________./public
|                         |
|                         |____index.html
|                         |____sw.js
|                         |____app.js
|
|________________________./model
|                         |
|                         |____subscribers_model.js
|
|________________________./router
|                         |
|                         |____push.js
|                         |____subscribe.js
|
|___________________________server.js 
```

现在从为 mongodb 数据库创建数据库模型开始。所以现在我使用已经安装在项目中的 **MongoDB** 的**mongose**ODM ORM 库

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const SubscriberSchema = new Schema({
    endpoint: String,
    keys: Schema.Types.Mixed,
    createDate: {
        type: Date,
        default: Date.now
    }
});

mongoose.model('subscribers', SubscriberSchema, 'subscribers'); 
```

现在让我们来看看配置文件

```
cd config 
```

然后打开已经在该文件夹中创建的 keys.js 文件

```
if (process.env.NODE_ENV === 'production') {
    module.exports = require('./keys_prod');
} else {
    module.exports = require('./keys_dev');
} 
```

并使用此代码更新您的 keys.js 文件，实际上此代码提供了生产和开发应用程序之间的数据库认证地址的智能切换。
在更新 keys_prod.js 和 keys_dev.js 文件之前，为客户端设备浏览器和运行应用程序的服务器之间生成 VAPID 密钥。
通过使用该命令

```
./node_modules/.bin/web-push generate-vapid-keys 
```

您将看到生成了两个密钥，一个是私钥，另一个是公钥
，如下所示。
[![create-vapid-key.png](../Images/236879638524d078e712c1ac85843895.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ItM2WagN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/Dw6GP2JWQhuPne1Iu074) 
复制两个密钥并粘贴到 keys_dev.js 或生产环境服务器配置中。

```
module.exports = {
//i used mlab database for fast and realiable pace development enviroment
    mongoURI: 'mongodb://web-push:webpush123@ds213053.mlab.com:13053/web-push',
    privateKey: 'ayTIBl3f0gcI-koFq-ZXPxSR4qicC0GcMNHA1dpHaj0' || process.env.VAPID_PRIVATE_KEY,
    publicKey: 'BK3Q7j8fcGFws03RiU5XakzDJ7KGEiRhdIX2H5U8eNmhhkdHT_j0_SD09KL96aFZOH_bsjr3uRuQPTd77SRP3DI' || process.env.VAPID_PUBLIC_KEY
} 
```

**process . env . vapid _ PUBLIC _ KEY**或**process . env . vapid _ PRIVATE _ KEY**理解为生产服务器运行环境配置。

> **注意:**请确保您始终为您的应用程序使用或生成您自己的 VAPID 密钥，以确保您的应用程序更加安全，并确保您的私钥不会暴露在网络上。

现在，所有重要的应用程序结构设置都已完成，现在开始在位于项目文件夹顶部的 **server.js** 中编码

```
const express = require('express');
const path = require('path');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');

require('./model/subscribers_model');

// Load Routes
const index = require('./router');

// subscriber route load push
const push = require('./router/push');

// subscriber route load
const subscribe = require('./router/subscribe');
// Load Keys
const keys = require('./config/keys');
//Handlebars Helpers

mongoose.Promise = global.Promise;

// Mongoose Connect
mongoose.connect(keys.mongoURI, {
        useMongoClient: true
    })
    .then(() => console.log('MongoDB Connected'))
    .catch(err => console.log(err));

//Create Express middleware
const app = express();
app.set('trust proxy', true);
// parse application/json
app.use(bodyParser.json());
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({
    extended: true
}));

// Set static folder
app.use(express.static(path.join(__dirname, 'public')));
// app.set('views', __dirname + '/public/js');

// Set global vars
app.use((req, res, next) => {
    res.locals.user = req.user || null;
    next();
});

// Use Routes

app.use('/', index);
app.use('/subscribe', subscribe);
app.use('/push', push);

// catch 404 and forward to error handler
app.use(function (req, res, next) {
    var err = new Error('Not Found');
    err.status = 404;
    next(err);
});

// error handlers

// development error handler
// will print stacktrace
if (app.get('env') === 'development') {
    app.use(function (err, req, res, next) {
        res.status(err.status || 500);
        res.render('error', {
            message: err.message,
            error: err
        });
    });
}

// production error handler
// no stacktraces leaked to user
app.use(function (err, req, res, next) {
    res.status(err.status || 500);
    res.render('error', {
        message: err.message,
        error: {}
    });
});

const port = process.env.PORT || 3000;

app.listen(port, () => {
    console.log(`Server started on port ${port}`);
}); 
```

现在来到文件夹 **router** 首先从已经通过命令创建的 subscribe.js 开始。在新标签中打开这个文件，然后将这段代码粘贴到你的 **subscribe.js** 文件中

```
const express = require('express');
const router = express.Router();
const mongoose = require('mongoose');
const Subscription = mongoose.model('subscribers');

//Post route of subscribe url is as http://host:3000/subscribe
router.post('/', (req, res) => {
    const subscriptionModel = new Subscription(req.body);
    subscriptionModel.save((err, subscription) => {
        if (err) {
            console.error(`Error occurred while saving subscription. Err: ${err}`);
            res.status(500).json({
                error: 'Technical error occurred'
            });
        } else {
            res.json({
                data: 'Subscription saved.'
            });
        }
    });
});

// fixed the error get request for this route with a meaningful callback
router.get('/', (req, res) => {
            res.json({
                data: 'Invalid Request Bad'
            });
});
module.exports = router; 
```

保存更改并移动到下一个文件 **push.js** ，通过命令行将此代码粘贴到您已经创建的 **push.js** 文件中

```
const express = require('express');
const router = express.Router();
const mongoose = require('mongoose');
const Subscription = mongoose.model('subscribers');
const q = require('q');
const webPush = require('web-push');
const keys = require('./../config/keys');
//Post route of push url is as http://host:3000/push
router.post('/', (req, res) => {
    const payload = {
        title: req.body.title,
        message: req.body.message,
        url: req.body.url,
        ttl: req.body.ttl,
        icon: req.body.icon,
        image: req.body.image,
        badge: req.body.badge,
        tag: req.body.tag
    };

    Subscription.find({}, (err, subscriptions) => {
        if (err) {
            console.error(`Error occurred while getting subscriptions`);
            res.status(500).json({
                error: 'Technical error occurred'
            });
        } else {
            let parallelSubscriptionCalls = subscriptions.map((subscription) => {
                return new Promise((resolve, reject) => {
                    const pushSubscription = {
                        endpoint: subscription.endpoint,
                        keys: {
                            p256dh: subscription.keys.p256dh,
                            auth: subscription.keys.auth
                        }
                    };

                    const pushPayload = JSON.stringify(payload);
                    const pushOptions = {
                        vapidDetails: {
                            subject: "http://example.com",
                            privateKey: keys.privateKey,
                            publicKey: keys.publicKey
                        },
                        TTL: payload.ttl,
                        headers: {}
                    };
                    webPush.sendNotification(
                        pushSubscription,
                        pushPayload,
                        pushOptions
                    ).then((value) => {
                        resolve({
                            status: true,
                            endpoint: subscription.endpoint,
                            data: value
                        });
                    }).catch((err) => {
                        reject({
                            status: false,
                            endpoint: subscription.endpoint,
                            data: err
                        });
                    });
                });
            });
            q.allSettled(parallelSubscriptionCalls).then((pushResults) => {
                console.info(pushResults);
            });
            res.json({
                data: 'Push triggered'
            });
        }
    });
});

// fixed the error get request for this route with a meaningful callback
router.get('/', (req, res) => {
    res.json({
        data: 'Invalid Request Bad'
    });
});
module.exports = router; 
```

再次确保将代码更改保存在您的 **push.js** 文件中，现在再次移动到 **index.js** 文件，并使用下面的代码更新代码

```
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
    res.locals.metaTags = {
        title: 'web-push-api',
        description: 'Web Push Notification Full Stack Application With Node Js Restful API',
        keywords: 'Web Push Notification Full Stack Application With Node Js Restful API',
        generator: '0.0.0.1',
        author: 'Saurabh Kashyap'
    };
    res.json({
        status: 'ok',
        message: 'Server is running'
    });
});

module.exports = router; 
```

并将 server.js 文件中的更改与上面的代码一起保存在 **server.js** 文件中，运行命令点击这个运行命令

```
node server.js 
```

请确保您在点击该命令后会看到这些消息。
[![server-running-command.jpg](../Images/2628083c82bebfde2ec9dfa3ad2e5ab4.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--vRIdlUeT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/Cr52K8NiQPOXAv6jOHSe)

检查应用程序运行正常后，再次按关闭应用程序。
那么现在服务器端运行应用程序代码就完成了。现在让我们开始下一步

## **第四步**

用公共名创建一个新文件夹，用下面的命令创建文件

```
mkdir public
cd public
touch index.html
touch sw.js
touch app.js 
```

[![create-public-dir.png](../Images/54d8b394ff29a184fdb8e645e3ba2ece.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OpyA7z5h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/FMH7LgpjQoiL2n5wFPtc)
[![creat-public-dir-files.png](../Images/ca478b8b4b569b3b3fd477f386e64826.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--tlUZlh48--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/tBj8EHNRZ5pYPfWDQhbA)

现在让我们将基本的 html 代码放在**index.html**文件中

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    Web-Push Application with Restful Api
</head>
<body>
    <h1>This is a web-push notification example</h1>
    <!-- call service worker for register and send subscribe request to the server with javascript -->
<script src="app.js"></script>
</body>
</html> 
```

保存此代码并移动到下一个文件 **app.js** 中，服务工作者浏览器检查并在浏览器中注册服务工作者，同时向应用 API**[http://host:3000/subscribe](http://host:3000/subscribe)**发送 ajax 请求，以便在客户端浏览器中订阅服务。

```
let isSubscribed = false;
let swRegistration = null;
let applicationKey = "put_your_public_key_here";

// Url Encription
function urlB64ToUint8Array(base64String) {
    const padding = '='.repeat((4 - base64String.length % 4) % 4);
    const base64 = (base64String + padding)
        .replace(/\-/g, '+')
        .replace(/_/g, '/');

    const rawData = window.atob(base64);
    const outputArray = new Uint8Array(rawData.length);

    for (let i = 0; i < rawData.length; ++i) {
        outputArray[i] = rawData.charCodeAt(i);
    }
    return outputArray;
}

// Installing service worker
if ('serviceWorker' in navigator && 'PushManager' in window) {
    console.log('Service Worker and Push is supported');

    navigator.serviceWorker.register('sw.js')
        .then(function (swReg) {
            console.log('service worker registered');

            swRegistration = swReg;

            swRegistration.pushManager.getSubscription()
                .then(function (subscription) {
                    isSubscribed = !(subscription === null);

                    if (isSubscribed) {
                        console.log('User is subscribed');
                    } else {
                        swRegistration.pushManager.subscribe({
                                userVisibleOnly: true,
                                applicationServerKey: urlB64ToUint8Array(applicationKey)
                            })
                            .then(function (subscription) {
                                console.log(subscription);
                                console.log('User is subscribed');

                                saveSubscription(subscription);

                                isSubscribed = true;
                            })
                            .catch(function (err) {
                                console.log('Failed to subscribe user: ', err);
                            })
                    }
                })
        })
        .catch(function (error) {
            console.error('Service Worker Error', error);
        });
} else {
    console.warn('Push messaging is not supported');
}

// Send request to database for add new subscriber
function saveSubscription(subscription) {
    let xmlHttp = new XMLHttpRequest();
    //put here API address
    xmlHttp.open("POST", "/subscribe");
    xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
    xmlHttp.onreadystatechange = function () {
        if (xmlHttp.readyState != 4) return;
        if (xmlHttp.status != 200 && xmlHttp.status != 304) {
            console.log('HTTP error ' + xmlHttp.status, null);
        } else {
            console.log("User subscribed to server");
        }
    };

    xmlHttp.send(JSON.stringify(subscription));
} 
```

现在保存所有文件，并开始为服务人员编码。让我们现在开始

```
let notificationUrl = '';
//notification registered feature for getting update automatically from server api
self.addEventListener('push', function (event) {
    console.log('Push received: ', event);
    let _data = event.data ? JSON.parse(event.data.text()) : {};
    notificationUrl = _data.url;
    event.waitUntil(
        self.registration.showNotification(_data.title, {
            body: _data.message,
            icon: _data.icon,
            tag: _data.tag
        })
    );
});

//notification url redirect event click
self.addEventListener('notificationclick', function (event) {
    event.notification.close();

    event.waitUntil(
        clients.matchAll({
            type: "window"
        })
        .then(function (clientList) {
            if (clients.openWindow) {
                return clients.openWindow(notificationUrl);
            }
        })
    );
}); 
```

保存所有代码。是..！！完成了。所以现在我们必须检查它是否工作。所以再次在您的终端中运行命令

```
node server.js 
```

现在在你的浏览器中打开**网址:[http://localhot:3000](http://localhot:3000)**
[![puh-popup.png](../Images/b16824b253eec0630b2b34d880dedc35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NRU0LQxs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/G7sZ1AuSyOF9DHyvcA2f)

点击“全部”后，您会在浏览器控制台中看到如下消息