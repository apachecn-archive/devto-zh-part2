# 对应用引擎用户使用 Firebase

> 原文：<https://dev.to/samthor/using-firebase-with-app-engine-users-3lnl>

如果您从 2008 年 App Engine 问世以来就一直在为它构建，那么您可能已经使用过它的授权流。很简单——在`app.yaml`中添加`login: required`,或者要求以编程方式登录——现在你就有了用户的谷歌账户。👍

> 问:我如何使用 Firebase，它有自己的认证系统？

很简单，您可以扔掉现有的授权流，直接使用 Firebase。然而，这将干扰您的用户，他们将不得不重新登录，这可能是意料之外的。😲

* * *

# 答案

答案是:**你不能**让你的用户用他们的谷歌账户自动登录 Firebase。你问:那我为什么要读这篇文章？！🤔

因此，尽管 Firebase 惊人地支持与多个提供商一起登录[——甚至是你的电话号码！📞—你实际上可以完全跳过 Firebase 的登录代码，直接创建用户，使用下图中的**优先策略**。](https://fir-ui-demo-84a6c.firebaseapp.com/)

[![Two possible Firebase user flows](img/87de5c130d4f1b6f21d358a66a0942fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xySucCJ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o2m0nu1jq7f6cokeea7y.png)

因此，我们可以创建全新的用户，并生成所谓的**自定义令牌**，供这些用户与 Firebase 的服务进行交互。这些用户在技术上与现有的 Google 账户没有关联——因为只有你的后端创建他们，你可以断言他们的有效性。

(第二种情况是更传统的模型——Firebase 有针对不同登录系统的“提供者”。但是，如果您使用的是现有的登录系统，*，即使*该登录系统“匹配”，您也不能迁移到这里。)

## 自定义令牌

要创建自定义令牌，可以使用 Firebase Admin SDK，它支持 Go，Node。JS，Python，Java。

首先——如果你使用的是`dev_appserver`,你可能需要设置你的服务帐户的凭证——这是实际登录你的帐户。前往 Firebase 控制台的“设置”“服务帐户”并下载私钥。在您的 shell 中，运行如下代码:

```
export GOOGLE_APPLICATION_CREDENTIALS="/full/path/to/file/serviceAccountKey.json"

# then you can start your app
dev_appserver.py app.yaml 
```

Enter fullscreen mode Exit fullscreen mode

> ⚠️永远不要把你的私人钥匙存放在公共场所或登记在册！

我将演示如何在 Go 应用程序中使用`CustomToken`。在代码中，您需要使用[应用引擎用户包](https://cloud.google.com/appengine/docs/standard/go/users/reference)来访问当前用户，将它的 ID 传递给 Firebase 的`CustomToken`方法:

```
// 0\. create firebase.App
app, err := firebase.NewApp(ctx, nil)
if err != nil { ... }

// 1\. get the current App Engine user
u := user.Current(ctx)
if u == nil || u.ID == "" { ... }

// 2\. create auth.Auth
client, err := app.Auth(ctx)
if err != nil { ... }

// 3\. use auth.Auth to mint a token for an ID, creating the account as nessecary
token, err := client.CustomToken(u.ID)
if err != nil { ... }

// 4\. send token to client- profit! 
```

Enter fullscreen mode Exit fullscreen mode

### 记住

*   这个`CustomToken`方法将**透明地**用你指定的 ID 创建一个用户帐号——*这是本指南的关键！*

*   虽然这个例子使用了`u.ID`(用户的电子邮件)，但是它可以是任何唯一的字符串。Firebase 的内置提供者使用[uuid](https://groups.google.com/forum/#!msg/firebase-talk/s9mv4S46Qs0/UXD4Tnl2BwAJ)，这看起来不像电子邮件地址，所以不可能有冲突。

## 客户端集成

一旦你制作了令牌，你需要把它发送给你的客户。我要谈的是网络，但是 [Firebase 的文档详细描述了原生客户端](https://firebase.google.com/docs/auth/admin/create-custom-tokens#sign_in_using_custom_tokens_on_clients)。

在我的项目中，我倾向于将令牌包含在第一个 HTML 响应中，这样您的代码就可以立即使用它——通过 JS 全局变量或属性:

```
<script>window.firebaseToken = '{{TOKEN}}';</script>
<!-- or -->
<body data-firebase-token="{{TOKEN}}"> 
```

Enter fullscreen mode Exit fullscreen mode

然后在您的 JS 中，您可以等待 auth 完成:

```
const token = window.firebaseToken || document.body.dataset.firebaseToken;
await firebase.auth().signInWithCustomToken(token);
const user = firebase.auth().currentUser;
console.info('user is', user); 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，您对 Firebase APIs 的任何请求——比如令人惊叹的可伸缩 Firestore 实时数据库——都将根据您刚刚创建的这个用户帐户进行身份验证。🎉

### 记住

*   因为这是我们创建的一个帐户，并不真正与 Google 登录相关联，所以这个令牌只适用于与 Firebase 对话。🔥

*   这个令牌一个小时后就过期了，你得马上用它去`signInWithCustomToken`。例如，如果令牌缓存在服务人员内部，这可能会影响到您。

* * *

就这样——你完成了！但是有两个扩展:

# 1️⃣多个授权提供商

Firebase 允许您将多个身份验证提供者链接到一个帐户。这给了你两个有趣的选择:

*   您可以从 App Engine 的授权流中迁移出来，因为用户会自然地再次登录和退出
*   你可以让用户用额外的账户登录，比如脸书，Twitter:这是 Firebase 认证模型的一个强大功能。

要了解更多关于账户链接的信息，请参见谷歌文档。

# 2️⃣声称

当您使用`CustomToken`创建账户时，您还可以注明“索赔”。这些基本上是您以后可以查找的标记，可能是在验证 Firebase 令牌时。一些例子可能包括“支付帐户”、“受限”等。

参见关于[创建自定义令牌](https://firebase.google.com/docs/auth/admin/create-custom-tokens#create_custom_tokens_using_the_firebase_admin_sdk)的全部文档。

* * *

# 感谢

我希望你已经学会了如何将你现有的 App Engine auth flow 与 Firebase 集成。如果您有任何问题，请在 Twitter 上联系我或在下面留言。✒️👇