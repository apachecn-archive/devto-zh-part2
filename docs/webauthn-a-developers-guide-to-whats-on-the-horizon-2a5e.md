# WebAuthn:开发人员对即将出现的东西的指南

> 原文：<https://dev.to/oktadev/webauthn-a-developers-guide-to-whats-on-the-horizon-2a5e>

最近有很多关于新的 W3C Web 认证 API(也称为 WebAuthn)的新闻。想知道这是怎么回事吗？让我们仔细看看。

Web 身份验证 API 允许浏览器使用硬件身份验证器，如 Yubikey 或手机的生物特征，如指纹读取器或面部识别。WebAuthn 可以与这些技术一起使用，以实现对网站的双重身份验证，甚至作为主要的身份验证机制。

开发该规范的大部分动机是为了减少对密码或其他容易被网络钓鱼的身份验证方法的依赖。这项工作始于 FIDO 联盟，随着主要浏览器供应商的加入，W3C 采用它来创建 WebAuthn 规范。

## web authn 是如何工作的？

在幕后，WebAuthn 规范使用[公钥加密](https://blog.vrypan.net/2013/08/28/public-key-cryptography-for-non-geeks/)为浏览器提供一种方法，使用操作系统或物理硬件令牌上存储的私钥来签署挑战。私钥永远不会离开设备，也永远不会提供给浏览器。对于像 [Yubikey](https://www.yubico.com/) 这样的硬件令牌，私有密钥甚至不能被底层操作系统访问！

如果您已经有了具有现有身份验证机制的现有用户，那么让用户注册 WebAuthn 凭据将会像这样工作:

*   让您的用户使用他们现有的凭据登录
*   创建新的 WebAuthn 凭据，并将其与用户帐户相关联
*   当用户返回时，提示他们输入 WebAuthn 凭据，一旦通过验证，您就可以让他们登录了

WebAuthn API 的一个独特属性是，它不能用于识别不同网站之间的用户。生成的凭据与生成它们的网站的域相关联。这为用户提供了额外的隐私层，因为网站不能使用 WebAuthn 凭据跨域识别用户。

### web authn JavaScript API

WebAuthn 规范定义了 web 应用程序可用的两个新的 JavaScript API:`navigator.credentials.create`和`navigator.credentials.get`。

您将使用`navigator.credentials.create`函数注册一个新的认证器，并将结果标识符存储在您的服务器上。要设置新的验证器，首先需要在服务器上生成 32 个随机字节，并将其提供给 JavaScript 函数。这 32 个随机字节是验证者将签名的数据的一部分，因此由您的服务器生成这一部分很重要，这样您的服务器就知道生成的签名质询是它发起的。

当您想让用户使用现有凭证重新登录时，您可以使用`navigator.credentials.get`功能要求用户证明拥有现有的注册设备。如果他们注册了多个设备，您可以向该函数传递多个标识符，匹配的标识符将被返回。

当站点运行`navigator.credentials.create`功能时，所有可用的 USB 密钥或其他认证机制将等待用户与之交互。就 Yubikeys 而言，你会看到所有当前插入的设备上都有闪烁的灯。如果您为`navigator.credentials.get`功能指定一个现有的标识符，那么只有那个键会闪烁。

请参阅 Mozilla 的《使用基于硬件令牌的 2FA 和 WebAuthn API 指南》,了解实现这一点所需的 JavaScript 代码的更多细节。

## 什么时候可以使用 WebAuthn？

截至 2018 年 4 月，WebAuthn API 在 Firefox Nightly 中可用，在 Chrome 中可以通过启用`chrome://flags`中的设置来打开。苹果尚未就 WebAuthn 是否或何时在 Safari 中可用发表评论。不过，他们参与了规范的开发，所以很可能很快就会发布。

该规范本身目前是 W3C 的“候选推荐标准”，是最终成为“推荐标准”的倒数第二步。

## 了解更多关于 WebAuthn 的信息

*   [通过 WebAuthn API 使用基于硬件令牌的 2FA](https://hacks.mozilla.org/2018/01/using-hardware-token-based-2fa-with-the-webauthn-api/)
*   [网络认证和 Windows Hello](https://docs.microsoft.com/en-us/microsoft-edge/dev-guide/device/web-authentication)
*   [FIDO2 项目](https://fidoalliance.org/fido2/)
*   了解更多关于 Okta 支持 WebAuthn 的计划