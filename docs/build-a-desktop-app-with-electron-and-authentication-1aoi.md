# 建立一个带有电子和认证的桌面应用程序

> 原文：<https://dev.to/oktadev/build-a-desktop-app-with-electron-and-authentication-1aoi>

Electron 是一个框架，用于使用 JavaScript、HTML 和 CSS 等 web 技术构建跨平台的桌面应用程序。它是为 GitHub 的 Atom 编辑器创建的，自那以后得到了广泛的采用。电子为我日常使用的几个应用提供动力:Slack、Kitematic 和 Visual Studio Code 等等。

电子 2.0 于 2018 年 5 月初发布，同时发布的还有对项目的更改，以遵守严格的语义版本化。这对开发者来说是个好消息，因为这意味着补丁发布会更加稳定，新功能只会出现在主要版本中。当开源项目正确地使用语义版本控制时，最终用户不会经常看到突破性的变化，并且倾向于高效。

用 web 技术开发桌面应用程序是一个吸引人的想法。在我看来，杰拉德·桑斯的这条推文一针见血:

液体错误:内部

你有网页开发技能吗？太好了！您已经具备了用 Electron 构建桌面应用程序所需的一切！

在本文中，我将向您展示如何使用 TypeScript、AppAuth-JS 和 OpenID Connect (OIDC)创建一个[电子](https://electronjs.org/)应用程序。您将了解如何为您的用户添加身份验证和保护电子应用程序。

## 什么是 AppAuth？

AppAuth 是一个旨在为本地应用创建客户端 SDK 的项目。它使得使用 OIDC 和 OAuth 2.0 在你的应用中实现认证和授权成为可能。它有适用于 iOS、macOS、Android 和 JavaScript 环境的 SDK。AppAuth-JS 是 JavaScript 客户端的 SDK。AppAuth 还支持到 OAuth 的 [PKCE 扩展](https://developer.okta.com/authentication-guide/auth-overview/#authorization-code-with-pkce-flow)，使公共客户端更加安全。

几乎每个应用程序都依赖于安全的身份管理系统。对于大多数开发电子应用程序的开发人员来说，他们需要在开发自己的身份验证/授权或者插入托管身份服务(如 Okta)之间做出决定。

## 用电子构建桌面应用

我会让你的事情变得简单。您将使用一个预先存在的示例，而不是从头构建一个应用程序。AppAuth-JS 项目有两个例子，一个是服务器端的例子，节点在 [src/node_app/index.ts](https://github.com/openid/AppAuth-JS/blob/master/src/node_app/index.ts) ，另一个是[app auth-JS-electronic-sample](https://github.com/googlesamples/appauth-js-electron-sample)。克隆电子例子开始。

```
git clone https://github.com/googlesamples/appauth-js-electron-sample.git okta-electron-example 
```

Enter fullscreen mode Exit fullscreen mode

打开该项目中的`package.json`,进行以下更改。

```
@@ -6,8 +6,8 @@
   "scripts": {
     "compile": "tsc",
     "watch": "tsc --watch",
- "start": "npm run-script compile && node_modules/.bin/electron .",
- "dev": "npm run-script watch & node_modules/.bin/electron ."
+ "start": "npm run compile && npx electron .",
+ "dev": "npm run compile && npm run watch & npx electron ."
   },
   "files": [
     "built/**"
@@ -22,10 +22,10 @@
     "@openid/appauth": "^0.3.5",
     "@types/react": "^16.3.17",
     "@types/react-dom": "^16.0.6",
- "electron": "^2.0.2",
     "material-design-lite": "^1.3.0"
   },
   "devDependencies": {
- "typescript": "^2.9.1"
+ "electron": "^2.0.2",
+ "typescript": "^3.0.3"
   } 
```

Enter fullscreen mode Exit fullscreen mode

这些变化不是必须的，但它们会让事情变得更容易。“脚本”中的变化使得编译将在运行`npm run dev`或`npm start`之前发生。您还将把`electron`依赖项移动为`devDependency`，并将 TypeScript 升级到最新版本。

导航到克隆的目录，安装 npm 的依赖项，然后运行应用程序。

```
cd okta-electron-example
npm i
npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

它应该启动应用程序，并显示一个登录链接。

[![blog/electron-react-appauth-js/initial-load.png](../Images/a7d725bf2b2708fc2319436bd90417b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MDWL3qpP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/electron-react-appauth-js/initial-load-d99e8f89254c517247bfd3334efbb227db0e524e09fc2321633ea2c7f6ce0bdd.png)

如果你有谷歌账户，点击**登录**，登录，你会被重定向回你的应用。你应该看到你的头像和名字显示出来。

[![After Google Sign-In](../Images/a2dbf0f7513a400313646dd2932d0c7a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0oI96A-t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/electron-react-appauth-js/after-sign-in-a7901afbf031985eb93357560c05e522989b34db6fb93b144b680ca4be65b9d5.png)

下图显示了如何使用 OpenID Connect 进行授权。

[![OIDC Flow](../Images/225ed4e7c8234e587e7ba7577abc869f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dx2fbxG_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/electron-react-appauth-js/oidc-flow-9bf5b49890556ed301b657901ce0ed7329749d0bb4f0d56297c65c54befe12e1.png)

此时，您可以看到 Google 的身份验证正在工作。在接下来的部分中，我将向您展示如何添加 PKCE 支持来使这个应用程序更加安全，以及如何使用 Okta 而不是 Google。

## 为什么要用 Okta 进行认证？

您可能会问:当使用 Google 进行身份验证时，我为什么要使用 Okta？原因很简单；如果你想管理你的应用程序的用户(你不可避免地会这样)，Okta 让它成为可能。使用谷歌，任何拥有谷歌账户的人都可以登录，但你没有办法撤销访问或更新用户权限，因为你不能通过谷歌管理你的用户。Okta 允许您管理您的用户，以及修改他们的属性和权限。更好的是，你仍然可以使用谷歌作为 Okta 的社交登录机制！

## 在桌面应用程序中使用 Okta 进行 OIDC 认证

Okta 的目标是让身份管理比你习惯的更容易、更安全、更可扩展。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

你准备好了吗？[今天就注册一个永远免费的开发者账户](https://developer.okta.com/signup/)！完成后，完成以下步骤来创建一个原生 OIDC 应用程序。

1.  在[developer.okta.com](https://developer.okta.com)上登录您的开发者账户。
2.  导航至**应用**并点击**添加应用**。
3.  选择**本地**，点击**下一步**。
4.  给应用程序起一个名字(例如，`My Electron App`)并添加`http://localhost:8000`作为登录重定向 URI。
5.  对于允许的授权类型，除了选择**授权码**外，还要选择**刷新令牌**。
6.  点击**完成**。

现在，您可以使用您的应用程序设置从使用 Google 更改为 Okta。修改`flow.ts`以使用你的 Okta 应用程序的设置。

```
const openIdConnectUrl = 'https://{yourOktaDomain}/oauth2/default';
const clientId = '{yourClientId}';
const redirectUri = 'http://localhost:8000'; 
```

Enter fullscreen mode Exit fullscreen mode

你还需要更新`app.ts`来使用你的应用的`/userinfo`端点。

```
let request =
    new Request('https://{yourOktaDomain}/oauth2/default/v1/userinfo', {
      headers: new Headers({'Authorization': `Bearer ${accessToken}`}),
      method: 'GET',
      cache: 'no-cache'
    }); 
```

Enter fullscreen mode Exit fullscreen mode

如果你重启应用并尝试登录，将会失败，因为你没有使用 PKCE。您将在启动的浏览器的地址栏中看到如下错误。

```
error=invalid_request&error_description=PKCE+code+challenge+is+required+when+the+token+endpoint+authentication+method+is+%27NONE%27. 
```

Enter fullscreen mode Exit fullscreen mode

## 为您的桌面应用添加 PKCE 支持

PKCE(发音为“pixy”)是 OAuth 2.0 的安全扩展，适用于移动(和桌面)客户端上的公共客户端。它旨在防止在同一设备上运行的恶意应用程序拦截授权代码。

```
 + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ +
    | End Device (e.g., Smartphone) |
    | |
    | +-------------+ +----------+ | (6) Access Token +----------+
    | |Legitimate | | Malicious|<--------------------| |
    | |OAuth 2.0 App| | App |-------------------->| |
    | +-------------+ +----------+ | (5) Authorization | |
    | | ^ ^ | Grant | |
    | | \ | | | |
    | | \ (4) | | | |
    | (1) | \ Authz| | | |
    | Authz| \ Code | | | Authz |
    | Request| \ | | | Server |
    | | \ | | | |
    | | \ | | | |
    | v \ | | | |
    | +----------------------------+ | | |
    | | | | (3) Authz Code | |
    | | Operating System/ |<--------------------| |
    | | Browser |-------------------->| |
    | | | | (2) Authz Request | |
    | +----------------------------+ | +----------+
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ + 
```

Enter fullscreen mode Exit fullscreen mode

PKCE 工作组提供了一个很好的解释，我已经在下面列出来了。你可以在[官方 RFC](https://tools.ietf.org/html/rfc7636) 中了解更多。

为了减轻这种攻击，PKCE 使用一种动态创建的加密随机密钥，称为“代码验证器”。为每个授权请求创建一个唯一的代码验证器，其转换后的值(称为“代码挑战”)被发送到授权服务器以获得授权代码。然后，所获得的授权码被发送到具有“代码验证器”的令牌端点，并且服务器将它与先前接收的请求码进行比较，以便它可以执行客户端拥有“代码验证器”的证明。这是一种缓解措施，因为攻击者不知道这个一次性密钥，因为它是通过 TLS 发送的，无法被截获。”

下图显示了 PKCE 如何使用你的应用程序和 Okta。

[![Auth Code Flow with PKCE](../Images/997b3307080692b467f08cd62bbf6f7d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e3EV4pdD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/oauth_auth_code_flow_pkce-7ebc49bec20d71423cb6b9e85139164988a2d021a37277733d04ffac8eaabaad.png)

现在你要将 PKCE 添加到你的电子应用程序中！在`flow.ts`中，为 PKCE 添加一个`challengePair`变量，作为`AuthFlow`类的成员变量。

```
private challengePair: { verifier: string, challenge: string }; 
```

Enter fullscreen mode Exit fullscreen mode

在构造函数的末尾添加一行来初始化这个变量。

```
this.challengePair = AuthService.getPKCEChallengePair(); 
```

Enter fullscreen mode Exit fullscreen mode

创建`pkce.ts`来定义`AuthService`类。

```
const crypto = require('crypto');

export class AuthService {

  static getPKCEChallengePair() {
    let verifier = AuthService.base64URLEncode(crypto.randomBytes(32));
    let challenge = AuthService.base64URLEncode(AuthService.sha256(verifier));
    return {verifier, challenge};
  }

  static base64URLEncode(str: Buffer) {
    return str.toString('base64')
      .replace(/\+/g, '-')
      .replace(/\//g, '_')
      .replace(/=/g, '');
  }

  static sha256(buffer: string) : Buffer {
    return crypto.createHash('sha256').update(buffer).digest();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

将该类的导入添加到`flow.ts` :

```
import { AuthService } from './pkce'; 
```

Enter fullscreen mode Exit fullscreen mode

在`makeAuthorizationRequest()`方法中，就在`if (username) {}`逻辑之后，将代码挑战和方法添加到`extras`图中。

```
// PKCE
extras['code_challenge'] = this.challengePair.challenge;
extras['code_challenge_method'] = 'S256'; 
```

Enter fullscreen mode Exit fullscreen mode

在`makeRequestTokenRequest()`中，添加一个`tokenRequestExtras`变量，并在请求中发送。

```
let tokenRequestExtras = { code_verifier: this.challengePair.verifier };

// use the code to make the token request.
let request = new TokenRequest(
  clientId,
  redirectUri,
  GRANT_TYPE_AUTHORIZATION_CODE,
  code,
  undefined,
  tokenRequestExtras
); 
```

Enter fullscreen mode Exit fullscreen mode

完成这些更改后，您应该可以登录了。然而，当你点击**用户信息**时，你将看不到你的用户名或头像。用**视图** > **打开 Chrome 开发者工具，切换开发者工具**看看为什么。

[![Electron's Developer Tools](../Images/236c08fa116d5c5bdf6710264b6f51ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z-I9IzrP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/electron-react-appauth-js/developer-tools-8419cf2ad4dd16e3c56867827289696f287ce6abc419df4594e41f56aa3a119a.png)

要解决这个问题，请更改`flow.ts`中的`scope`变量，使其包含`profile`。同时，添加`offline_access`，这样你的应用就可以在没有互联网连接的情况下工作。

```
const scope = 'openid profile offline_access'; 
```

Enter fullscreen mode Exit fullscreen mode

刷新你的 app(Mac 上 Command+R，Windows/Linux 上 Ctrl+R)，现在点击**用户信息**就应该看到名字了。

[![Name from User Info Endpoint](../Images/796d9dbb0e9571fcc53f57af130c14b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IoKrMxqf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/electron-react-appauth-js/userinfo-name-f4bce0776429b416c7cb27ba3a5921c4a76e824bebf97a6e01b2d0d11f787a9c.png)

**注意:**我利用[这些 PKCE 代码样本](https://github.com/openid/AppAuth-JS/issues/28)来完成所有这些工作。

### 在 Okta 添加头像

您可能会注意到用户信息端点不返回头像。`app.ts`中的代码基于一个`picture`属性设置化身。

```
private updateUi() {
  this.handleSignIn.textContent = SIGN_OUT;
  this.fetchUserInfo.style.display = '';
  if (this.userInfo) {
    this.userProfileImage.src = `${this.userInfo.picture}?sz=96`;
    this.userName.textContent = this.userInfo.name;
    this.showSnackBar(
        {message: `Welcome ${this.userInfo.name}`, timeout: 4000});
    this.userCard.style.display = '';
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*你可以删除上面代码中的`?sz=96`,因为这个例子没有用到它。*

要为您的用户添加一个`picture`属性，请登录您的 Okta 仪表板并导航至**用户** > **个人资料编辑器**。单击第一个“用户”并添加一个`picture`属性。点击**保存**。

[![Add Picture Attribute](../Images/288c246592c14246d9ba342a27743968.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KNx4GxTn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/electron-react-appauth-js/add-attribute-92ce1f547cffc692375ce3bf5bc3edbfadd466e0091f67cc921685615ce4ea0d.png)

导航回配置文件编辑器，点击电子应用程序的**映射**。创建从`user.picture`到`picture`的映射，并选择在用户创建和更新时应用该映射。点击**保存映射**和**立即应用更新**。

[![Add Picture Mapping](../Images/ce35e65378f054786dfa47bfb6263a1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FbcVcH2U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/electron-react-appauth-js/add-mapping-befaa43f84f9c2119a2b6b4ee283984cd78d63240a86eb90058949ce46f8c668.png)

现在转到**用户** > **人**，选择一个用户，导航到**个人资料**标签，点击**编辑**。在底部为`picture`添加一个值。比如你可以用我们 Okta 开发者 logo 的网址。

```
https://www.okta.com/sites/all/themes/Okta/images/logos/developer/Dev_Logo-02_Large.png 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你点击用户信息链接，你应该会看到一个与你的用户相关的头像。

[![User Info Picture](../Images/24588b201d3970873e2055f9dc053cc0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--arnKmnO5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/electron-react-appauth-js/userinfo-picture-772fce4b2aec8366fb32036c35741c379b6f3b4b7ccfa78d3b6fbf5759ff7871.png)

**提示:**如果你打算在制作中使用它，我建议你使用较小的图片(例如，150x150 的尺寸)。你也可以 [base64 编码](https://www.base64-image.de/)一个图像，并将其值用于你的图片。

下面是一个截图，显示了一些额外的波兰和我的帐户 base64 图像值的应用程序。

[![Polished Matt](../Images/4e478c5bb5589c90af9fa961cba69f92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--heEp15vq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/electron-react-appauth-js/polished-matt-9566a63af2e502bff578c4811c8b8470037d45fb3ded934264365ec495a5d7df.png)

## 将您的桌面应用打包用于生产

要打包这个应用程序进行生产分发，您可以使用[电子生成器](https://www.electron.build/)。用 npm 安装电子生成器。

```
npm i -D electron-builder@20.28.4 
```

Enter fullscreen mode Exit fullscreen mode

给你的`package.json` :
添加一个`build`部分

```
"build": {
  "appId": "com.okta.developer.electron",
  "productName": "Electron Awesomeness",
  "mac": {
    "category": "public.app-category.developer-tools"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后添加`pack`、`dist`和`postinstall`脚本。

```
"scripts": {
  ...
  "pack": "npm run compile && electron-builder --dir",
  "dist": "npm run compile && electron-builder",
  "postinstall": "electron-builder install-app-deps"
} 
```

Enter fullscreen mode Exit fullscreen mode

要将您的应用打包用于生产，请使用以下命令:

*   `npm run pack`会生成包目录，但不会真正打包。这对于测试非常有用。
*   `npm run dist`将以可分发的格式打包(例如，dmg、Windows installer、deb 包)。

**注意:**如果 app 打包后不启动，很可能是因为你没有配置[代码签名](https://www.electron.build/code-signing)。要在构建 macOS 时禁用代码签名，请运行`export CSC_IDENTITY_AUTO_DISCOVERY=false`。如果您有 Apple 开发者帐户，请打开 Xcode，前往**偏好设置** > **帐户**，确定您已登录，并且您的开发证书已下载。

## 电子示例 App 源代码

你可以在[https://github . com/okta developer/okta-app auth-js-electronic-example](https://github.com/oktadeveloper/okta-appauth-js-electron-example)找到本文的源代码。

我做了一些小的调整(例如，优化导入，将双引号改为单引号)，但没什么大的。要查看此项目与原始项目的不同之处，[单击此处](https://github.com/googlesamples/appauth-js-electron-sample/compare/master...oktadeveloper:master)。

## 了解更多关于电子、AppAuth 和 OIDC 的信息

你有它！向前迈进，用你的网络技能创造惊人的桌面应用程序！

要了解更多关于电子、AppAuth、React 和 OIDC 的信息，请查看以下资源:

*   [编写你的第一个电子应用](https://electronjs.org/docs/tutorial/first-app)
*   [用 Kotlin 在 Android 中构建一个基本的 CRUD 应用](https://dev.to/blog/2018/09/11/android-kotlin-crud)
*   [使用 React、GraphQL 和用户认证构建健康跟踪应用](https://dev.to/blog/2018/07/11/build-react-graphql-api-user-authentication)
*   [身份、声明、&令牌 OpenID Connect Primer，第 1 部分，共 3 部分](https://dev.to/blog/2017/07/25/oidc-primer-part-1)

有问题吗？请在下面留下你的评论，或者在我们的[开发者论坛上提问。](https://devforum.okta.com/)

喜欢你在这里学到的吗？关注 [@oktadev](https://twitter.com/oktadev) ，在脸书上喜欢我们[，在 LinkedIn](https://www.facebook.com/oktadevelopers/) 上关注我们[，或者](https://www.linkedin.com/company/oktadev/)[在 YouTube](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q) 上观看我们的视频。