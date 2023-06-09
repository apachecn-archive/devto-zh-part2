# AsyncResolver.js 简介:解析异步订阅的决策

> 原文：<https://dev.to/apoorv_saxena/introducing-asyncresolverjs-resolve-async-subscribed-decisions-3ci1>

> PubSub 架构实现了一个不连贯的范例，订阅者只需在事件发布时进行监听。有些情况下，我们希望维护一个事件的多个侦听器/订阅者，尽管我们希望根据订阅者的反应来采取行动。

### 举例

让我们考虑这样一种情况，一个网页上有几个组件，用户可以改变它们的状态，我们让每个组件都订阅为侦听器来侦听页面转换，以便我们可以检查用户是否试图移动而不保存数据。

现在，当用户点击一个链接时，我们发布一个事件，提到用户从页面的转换，尽管我们想询问每个侦听器(或组件)用户是否对他们的状态做了任何更改，并且在没有保存它们的情况下移动。

如果在任何组件中有任何未保存的更改，那么我们取消转换，而是向用户显示一个信息对话框，要求他在继续下一步之前保存信息。

[![AsyncResolver.js Asynchronously resolve subscribed decisions](img/89c46116c3c29096f91cbddbe462653c.png "AsyncResolver.js Asynchronously resolve subscribed decisions")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nwNvuWrf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/972g7aw2yqoo34qvrgzq.png)

AsyncResolver.js 是针对这一需求的解决方案，它融合了发布子架构，并承诺在异步环境中提供决策能力。

### 安装

```
### NPM
npm install async-resolver

### Yarn
yarn add async-resolver 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

```
const AsyncResolver = require('async-resolver');

let resolver = new AsyncResolver();
resolver.subscribe('locationChange', () => Promise.resolve());
resolver.subscribe('locationChange', () => Promise.reject());

resolver
    .publish('locationChange', {
        promiseMethod: 'any'
    })
    .then(() => console.log('location change allowed'))
    .catch(() => console.log('location change denied')) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

详情请查看 Github 上的 [AsyncResolver.js repo](https://github.com/ApoorvSaxena/async-resolver)