# Azure vs GCP 第 18 部分:移动应用开发(GCP)

> 原文：<https://dev.to/kenakamu/azure-vs-gcp-part-18-mobile-app-development-gcp-2j1h>

我在[上一篇文章](https://dev.to/kenakamu/azure-vs-gcp-part-17-mobile-app-development-4j5k)中测试了 Azure 移动应用，但我完全忘记了[“应用中心”](https://appcenter.ms/)，这是 Azure 为移动应用开发提供的新服务。然而，我在本文中测试了 GCP 解决方案，稍后再回到移动中心。

# 火基

Firebase 是一个开发者可以轻松开发移动(iOS 和 Android)和 Web app 的平台。

当我开始学习谷歌云时， [Firebase](https://firebase.google.com/) 是我有机会尝试的第一项服务，后来我意识到谷歌拥有大量的服务作为谷歌云平台。

这可能会引起一点混乱，因为 GCP 和 Firebase 目前是两个独立的服务，但谷歌在这两者之间进行了许多整合，最终它可能会成为一个服务。

### 开发者的关键特性

它为移动应用程序开发提供了几个关键特性。

*   多平台:提供 iOS/Android/Web/Unity 的 SDK。它还提供了用于 iOS/Android 的 C++ SDK 和用于管理目的的 SDK。
*   身份验证:它支持各种提供者和用户存储。
*   数据访问:数据有几种选择。实时数据库、Firestore 和存储。
*   离线:支持离线模式和同步功能，当回到在线实时数据库和 Firestore
*   通知:通知移动设备。
*   托管和功能:它还为静态内容和功能提供托管。

### 主要功能为操作

除了开发人员体验之外，它还提供了以下操作特性:

*   自动缩放:一些服务具有自动缩放功能。
*   暂存:主机提供暂存功能。

# 试试吧！

对 C#开发者来说很不幸的是，我找不到一个官方的 SDK 和教程。所以在这种情况下，我使用 Node.js(来尝试 web)。

### 创建 firebase 项目并安装 SDK

首先，我需要创建一个 Firebase 项目。

1.进入 [Firebase 控制台](https://console.firebase.google.com)并使用谷歌账户登录，然后点击“添加项目”。

[![console](../Images/16a3531de47b1754ef804b9668da7355.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bhcx5fV8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3sj0qo83ryqkovbbsiot.PNG)

2.输入项目名称并创建项目。你也可以链接到 GCP 项目。

[![console](../Images/89fc523dce000b7ab5276a80aaf773f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0DSFhHIp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q0o1cc6zbyolnc7h5066.PNG)

3.打开命令提示符或控制台，并运行以下命令。

```
npm install -g firebase-tools 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 web 应用项目

接下来，创建 web 应用程序项目。

1.运行以下命令创建 web 应用程序并初始化 firebase。

```
mkdir firebase-web
cd firebase-web
firebase init 
```

Enter fullscreen mode Exit fullscreen mode

2.Firebase SDK 初始化项目。选择数据库和托管。

[![init](../Images/32315a66c8cc6aecd09f140e5833f738.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OO0XZd-d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3l88q1ohpcxammc5wc7e.PNG)

3.然后，选择要关联的 firebase 项目。我可以从这里创建新项目，但我仍然需要在控制台中手动创建 firebase 项目。

[![init](../Images/d0499300fe4de933711cdf596ba84ab9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qu2mwoD7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fse2xumra6k3c4lpipwc.PNG)

4.选择数据库和主机的默认选项，并完成初始化。运行以下命令为应用程序提供服务。

```
firebase serve 
```

Enter fullscreen mode Exit fullscreen mode

5.访问中显示的 URL 以确认它已启动并正在运行。 [http://localhost:5000](http://localhost:5000) 默认。

[![init](../Images/a79bf81599dcaeeaf38bc3757483e18a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sEGfaif8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/clof90bg54ruyw6zvozr.PNG)

6.停止该服务，并通过 Visual Studio 代码打开文件夹。

7.默认情况下，所有的脚本都嵌入在 html 文件中。将 main.js 文件添加到公共文件夹。

8.从 firebase SDK 代码所在的 index.html“剪切”脚本，改为链接到 main.js。

9.在 main.js 中创建即时函数

```
(function(){

    document.addEventListener('DOMContentLoaded', function() {
         try {
          let app = firebase.app();
          let features = ['auth', 'database', 'messaging', 'storage'].filter(feature => typeof app[feature] === 'function');
          document.getElementById('load').innerHTML = `Firebase SDK loaded with ${features.join(', ')}`;
        } catch (e) {
          console.error(e);
          document.getElementById('load').innerHTML = 'Error loading the Firebase SDK, check the console.';
        }
      });

}()); 
```

Enter fullscreen mode Exit fullscreen mode

10.对 css 做同样的操作，并再次运行“firebase serve ”,以确认它仍然按预期运行。

# 实时数据库

虽然有几个存储数据的其他选择，我在这里使用实时数据库，因为它是我理解的最基本的选择。

1.首先，启用实时数据库。转到 firebase 控制台，选择“数据库”，然后单击“开始”实时数据库。

[![console](../Images/6b50238391851767d59684d2701a280c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ySIlHKkR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fj299jpu63tbrxeeepp6.PNG)

2.选择“测试模式”,打开安全测试并启用它。

[![console](../Images/37108dd0324cb1832393ecbba495b197.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tF7EB3FP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pcbss06tyz87tx47j99z.PNG)

3.单击“添加子代”添加字段。

[![console](../Images/2ef6f9dc3fd56d4fc5e9d1338ba00106.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--icrIJNXJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/haojduy2u0yjti4c4t5x.PNG)

4.添加“文本”字段并输入任意值。

[![console](../Images/ab16f8eb79533ceb7d8dc868fdf4c0fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yny7K5mx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/drttm9uolwmzy24e4jry.PNG)

5.编辑 main.js，如下所示。

```
(function () {

    document.addEventListener('DOMContentLoaded', function () {
        try {
            let txtDB = document.getElementById('txtDB');
            let dbRef = firebase.database().ref().child('text');
            dbRef.on('value', snap => txtDB.innerText = snap.val());
        } catch (e) {
            console.error(e);
        }
    });
}()); 
```

Enter fullscreen mode Exit fullscreen mode

6.更新 index.html 体。

```
<body>
  <h1 id="txtDB"></h1>
  </div>
  <script defer src="/main.js"></script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

7.全部保存并刷新页面。我可以从数据库中看到的价值。

[![app](../Images/5f1f0ce72a481fff5417076175d9a3c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1C5AVYck--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4lcnm1usk63xxqbi22wm.PNG)

8.更改数据库中的值，并确认更改实时反映。

# 认证

默认情况下，Firebase 支持多个身份验证提供者。这一次，我像在 Azure 中一样使用脸书登录。此外，firebase 还提供了 SDK [firebase-ui](https://github.com/firebase/firebaseui) ，其中包括用于身份验证的 ui。我这次用的是这个 sdk。

1.首先，在控制台上启用身份验证。转到 Firebase 控制台，选择“身份验证”，然后单击“设置登录方法”。

[![auth](../Images/0ed4746ed8857be3e53cc6b83a600566.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6LfoACcZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lartrha5wyh50pygnhny.PNG)

2.点击“脸书”勾选启用。然后添加屏幕上显示的必要信息。

[![auth](../Images/89d777e709ef88b765a83c5350f5ef8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tAoIe9Uq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ri2xb0ar8hnpbigwsj9u.PNG)

3.替换 main.js 中的代码以使用 firebase auth。

```
(function () {

    document.addEventListener('DOMContentLoaded', function () {
        try {
            let txtDB = document.getElementById('txtDB');
            let dbRef = firebase.database().ref().child('text');
            dbRef.on('value', snap => txtDB.innerText = snap.val());

            // Attach click event to sign-out button
            document.getElementById('sign-out').addEventListener('click', function () {
                firebase.auth().signOut();
            });

            // Monitor authentication status change.
            firebase.auth().onAuthStateChanged(function (user) {
                if (user) {
                    // User is signed in.
                    var displayName = user.displayName;
                    var email = user.email;
                    var emailVerified = user.emailVerified;
                    var photoURL = user.photoURL;
                    var uid = user.uid;
                    var phoneNumber = user.phoneNumber;
                    var providerData = user.providerData;
                    user.getIdToken().then(function (accessToken) {
                        document.getElementById('account-details').textContent = JSON.stringify({
                            displayName: displayName,
                            email: email,
                            emailVerified: emailVerified,
                            phoneNumber: phoneNumber,
                            photoURL: photoURL,
                            uid: uid,
                            accessToken: accessToken,
                            providerData: providerData
                        }, null, '  ');
                    });
                    document.getElementById('firebaseui-auth-container').style.display = 'none';
                    document.getElementById('sign-out').style.display = 'block';                    
                } else {
                    // User is signed out.
                    document.getElementById('firebaseui-auth-container').style.display = 'block';
                    document.getElementById('sign-out').style.display = 'none';
                    document.getElementById('account-details').textContent = 'null';
                }
            }, function (error) {
                console.log(error);
            });

            // Create Firebase UI Configuration
            var uiConfig = {
                signInSuccessUrl: 'index.html',
                signInOptions: [
                    firebase.auth.FacebookAuthProvider.PROVIDER_ID
                ]
            };

            // Initialize the FirebaseUI Widget using Firebase.
            var ui = new firebaseui.auth.AuthUI(firebase.auth());
            // The start method will wait until the DOM is loaded.
            ui.start('#firebaseui-auth-container', uiConfig);
        } catch (e) {
            console.error(e);
        }
    });
}()); 
```

Enter fullscreen mode Exit fullscreen mode

4.更新 index.html 以包含 firebase ui sdk。在标题部分，添加以下内容。

```
<script src="https://cdn.firebase.com/libs/firebaseui/2.7.0/firebaseui.js"></script>
<link type="text/css" rel="stylesheet" href="https://cdn.firebase.com/libs/firebaseui/2.7.0/firebaseui.css" /> 
```

Enter fullscreen mode Exit fullscreen mode

5.然后在正文中包含必要的元素。

```
<body>
  <h1 id="txtDB"></h1>
  <div id="firebaseui-auth-container"></div>
  <div id="account-details"></div>
  <script defer src="/main.js"></script>
  <button id="sign-out">SignOut</button>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

6.全部保存并刷新浏览器。我可以看到脸书登录按钮。

[![auth](../Images/3aca12345733b9e86f5346e4b4a24f63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--shQeK6LN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/niz1fy0135nkjsqooi9z.PNG)

7.登录到脸书，并确认我得到我的登录信息。

[![auth](../Images/4f01040707fe4190f3ba51c15e5b1bd7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e4lJRYb3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0ycqj7g25vzfki4npc7u.PNG)

8.单击“注销”按钮注销。

9.返回 Firebase 控制台，然后转到身份验证|用户。我可以看到我刚刚登录的用户已经创建。

[![auth](../Images/30efb41b4cf4591806e9b2714f7f66cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--35kQZpec--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e9qv6ifw3dcokxzujp75.PNG)

# 托管

现在 web 应用程序有了数据库和认证。让我们将它部署到托管服务器。

1.运行以下命令。

```
firebase deploy 
```

Enter fullscreen mode Exit fullscreen mode

2.部署完成后，对 URL 的访问显示为部署命令的结果。

[![deploy](../Images/1ff1e85a7942203712e9e5f76b135121.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g5nXze0_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gqnkxz8skhsh26fr5pqs.PNG)

3.返回控制台并选择“托管”以确认信息。

[![deploy](../Images/3bf665d897d30a894f1278a564125843.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tWz6Eyof--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n9acfqpkpf0rmwsm9049.PNG)

# 消息/推送通知

接下来，添加推送通知。

### 后台工作人员

即使浏览器不活动，用户也应该收到通知。为此，我使用后台工作程序。

1.在公共文件夹下添加 firebase-messaging-sw.js 文件。

[![push](../Images/a9f328d3a5b54fd80b0dedc0e25995ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5zVWqkDe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hrb60llkbw1dfc0kj65h.PNG)

2.添加以下脚本。

```
// Give the service worker access to Firebase Messaging.
// Note that you can only use Firebase Messaging here, other Firebase libraries
// are not available in the service worker.
importScripts('https://www.gstatic.com/firebasejs/4.8.1/firebase-app.js');
importScripts('https://www.gstatic.com/firebasejs/4.8.1/firebase-messaging.js');

// // Initialize the Firebase app in the service worker by passing in the
// // messagingSenderId.
// firebase.initializeApp({
//   'messagingSenderId': '826314837287'
// });

// Retrieve an instance of Firebase Messaging so that it can handle background
// messages.
const messaging = firebase.messaging(); 
```

Enter fullscreen mode Exit fullscreen mode

### 注册设备以通知

首先，我需要知道通知发送到哪里。为此，我可以请求用户允许这样做，并获得唯一的 id。

1.要发送通知，我需要获得用户的许可，并跟踪可以推送通知的设备。更新 index.html 以包含按钮，让用户订阅通知。

```
<body>
  <h1 id="txtDB"></h1>
  <div id="firebaseui-auth-container"></div>
  <button id="subscribe" style="display:none">Subscribe for notification</button>
  <button id="unsubscribe" style="display:none">Unsubscribe for notification</button>

  <div id="account-details"></div>
  <script defer src="/main.js"></script>
  <button id="sign-out">SignOut</button>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

2.然后在加载的事件中添加脚本。首先，定义每个按钮。

```
let sucscribe = document.getElementById('subscribe');
let unsubscribe = document.getElementById('unsubscribe'); 
```

Enter fullscreen mode Exit fullscreen mode

3.还定义了 messing 对象

```
const messaging = firebase.messaging(); 
```

Enter fullscreen mode Exit fullscreen mode

4.为 subscribe 添加 click 事件侦听器，以创建对数据库的订阅。getToken 函数返回设备唯一 id。

```
subscribe.addEventListener('click', () => {
    messaging.requestPermission()
        .then(() => messaging.getToken())
        .then((token) => {
            if (token) {
                firebase.database().ref('/tokens').push({
                    token: token,
                    uid: firebase.auth().currentUser.uid,
                    name: firebase.auth().currentUser.displayName
                });
                checkSubscriptionStatus();
            } else {
                console.log('No permission granted');
            }
        })
        .catch((err) => console.log(err));
} 
```

Enter fullscreen mode Exit fullscreen mode

5.添加取消订阅的单击事件以从数据库中删除令牌。这一次，从数组中获取项目并移除它。

```
unsubscribe.addEventListener('click', () => {
    messaging.getToken()
        .then((token) => {
            if (token) {
                messaging.deleteToken(token).then(() => {
                    firebase.database().ref('/tokens').orderByChild('uid').equalTo(firebase.auth().currentUser.uid)
                        .once('value').then((snapshot) => {
                            firebase.database().ref('/tokens').child(Object.keys(snapshot.val())[0]).remove();
                        })
                });
                checkSubscriptionStatus();
            } else {
                console.log('No permission granted');
            }
        })
        .catch((err) => console.log(err));
}); 
```

Enter fullscreen mode Exit fullscreen mode

6.添加检查当前订阅状态的 checkSubscriptionStatus 函数。

```
// Check the subscription status
function checkSubscriptionStatus() {
    // Check database if user is signed in
    if (firebase.auth().currentUser) {
        firebase.database().ref('/tokens').orderByChild('uid').equalTo(firebase.auth().currentUser.uid)
            .once('value').then((snapshot) => {
                if (snapshot.val()) {
                    unsubscribe.style.display = 'block';
                    sucscribe.style.display = 'none';
                } else {
                    unsubscribe.style.display = 'none';
                    sucscribe.style.display = 'block';
                }
            });
    }
    // Otherwise, hide them all.
    else {
        unsubscribe.style.display = 'none';
        sucscribe.style.display = 'none';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

7.要使状态检查在登录和注销时都有效，请在 onAuthStateChanged 中添加函数。把它加在事件的最后一行。

8.订阅 onMessage 事件以获取通知。

```
messaging.onMessage(function (payload) {
    console.log('Message received. ', payload);
}); 
```

Enter fullscreen mode Exit fullscreen mode

9.全部保存并刷新浏览器。如果尚未登录，请单击“订阅”按钮，并确认令牌在数据库端有值。

[![push](../Images/78f5dc1d1caaa6d391f14e2645bc90b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n9HkqC2H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ov6qm2pyumb40dqkejyp.PNG)

10.点击“取消订阅”按钮确认该值被删除。

### 发送推送通知

有几种方法可以发送通知，比如使用函数，但是这次，我使用 postman 来简化这个过程。

1.首先，获取特征。转到控制台并选择项目设置。然后复制“服务器密钥”。

2.打开 postman 并使用以下设置。

*   地址:[https://fcm.googleapis.com/fcm/send](https://fcm.googleapis.com/fcm/send)
*   HTTP 动词:POST
*   标题:授权:密钥=
*   标题:内容类型:应用程序/json

[![postman](../Images/330312156fd090e985c1e0d709ef734d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_6WbT8mv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w82wgpt2980yso2rhurf.PNG)

3.按如下方式指定主体。当您订阅推送通知时，用存储在数据库中的令牌替换“to”值。您也可以更改任何值。对于图标，我使用我的 dev.to 图标，并指定单击本地主机地址的动作。

```
 {  "notification":  {  "title":  "Message Title",  "body":  "Message body",  "icon":  "https://thepracticaldev.s3.amazonaws.com/uploads/user/profile_image/56333/b0f73a4f-9243-4cda-9aaf-be1a87b3ee88.png",  "click_action"  :  "http://localhost:5000"  },  "to"  :  <token>  } 
```

Enter fullscreen mode Exit fullscreen mode

4.打开浏览器，进入 [http://localhost:5000](http://localhost:5000) ，然后按 F12 打开开发者控制台。然后从邮递员那里发送通知。您可以在控制台中看到收到的消息。

[![push](../Images/03956ae2d22355d5ed5c99e369af4c87.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YndwnZ9H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/443ewg9do70v72v2rv5c.PNG)

5.最小化浏览器并再次发送通知。这一次，您会在操作系统中看到推送通知弹出窗口。

[![push](../Images/811fa860a97f178a4f5e29c28ac6b2ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RkeVjgL1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9m9lw9ty3gwmpnxfpdgh.PNG)

6.点击通知并确认 [http://localhost:5000](http://localhost:5000) 已打开。

# 总结

唷，这是一次漫长的考试！！！我的观点在尝试了 Azure 和 GCP (Firebase)之后，两个平台都有自己的好处。

[天蓝色]

*   它提供了移动应用程序所需的所有基本功能。
*   因为它使用普通的 Azure 资源，并提供易于理解的快速入门示例，所以对我来说非常简单。
*   通过使用其他 Azure 组件来扩展服务也更容易，因为它使用普通的 Azure 资源。
*   测试和部署等移动特定功能的运气，这实际上是由应用中心涵盖的。
*   它支持 C#:)

[火焰基地]

*   它提供了移动应用程序所需的所有基本功能。
*   它很容易玩，因为所有的东西都在一个地方，然而，如果你从 GCP 出发，就不容易找到。我希望 firebase 迟早成为 GCP 的一部分。
*   GitHub 中的优秀样本。
*   为手机提供更多有用的服务，如测试、广告、动态链接等。
*   与 GCP 服务集成的灵活性降低或难度增加。

所以，如果你的项目需要 C#或 windows 应用支持其他服务，我强烈推荐 Azure。另一方面，如果你的项目纯粹是为了移动应用，或者当你需要快速启动时，我推荐 GCP，因为它更容易用优秀的样本组织组件。

为了将来的测试、调试、部署、报告和分析，我对这两种服务做了进一步的研究。

# 参考文献

[基于 Firebase 的 Web 推送通知](https://www.youtube.com/watch?v=XdzXaW8IbBM&list=PLGVwFLT24VFq3ZTcakcpByFhe1ex1BPuN)
[基于 Web 的 Firebase-教程](https://www.youtube.com/watch?v=2Vf1D-rUMwE&list=PLl-K7zZEsYLmnJ_FpMOZgyg6XcIGBu2OX)