# 超越登录屏幕-第二部分

> 原文：<https://dev.to/shyamala_u/beyond-the-login-screen---part-ii--450n>

在[之前的](https://dev.to/shyamala_u/beyond-the-login-screen---part-i--29g3)帖子中，我们讨论了为什么收集用户数据时透明度很重要。在这一部分中，让我们更深入地研究开放标准 OpenID Connect，看看该标准如何在用户数据管理方面提供灵活性和透明性。

## 熟悉的 OAuth2 舞蹈

OpenID Connect 构建在 OAuth2 之上，

[![](../Images/5bc347f7c77e3b0d83e166b229239c05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_FF-J4zQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q6qv6y3adoe6zwakir2n.png)

在开始讨论 OpenID Connect 的技术细节之前，让我们回顾一下我们是如何习惯于登录谷歌和 T2 见面的。

**[移动 1](#m1) :** 用户进入`Meetup` app，点击`Google`登录

**[Move 2](#m2) :** `Meetup` app 然后要求`Google`对用户进行认证，并分享用户的`basic profile`

**[移动 3](#m3) :** `Google`然后要求`user`登录

**[移动 4](#m4) :** 在`user`、`Google`成功认证后，现在询问`User`他们是否可以与`Meetup`共享他们的`basic profile`，并且`User` **同意**到`Google`将他们的数据共享给`Meetup`

**[招式 5](#m5) :** `Google`现在与`Meetup`共享一个临时密钥，可以解锁`User's` **同意**数据

**[移动 6](#m6) :** `Meetup`使用该键获取`Basic profile`数据。

## 什么用户数据？

在 [Move 2](#m2) ，`Meetup`知道它只需要`basic profile`的数据如`Name`，`email`和`profile picture`来完成它的用户档案。

## 为什么是用户数据？

在[移动 4](#m4) ，`Google`请求`User's` **同意**将数据共享给`Meetup`，解释这将被用作简档。

## 灵活性和透明度

现在，将`Google`替换为您自己的身份提供者，您就实现了更高水平的透明数据管理灵活性。

假设 Meetup 需要这个新功能，它可以显示附近的 Meetup，所以现在 Meetup 应用程序需要知道用户的邮政编码，Meetup 应用程序现在需要做的就是请求新的范围`pincode`和`basic_profile`，现在身份提供者将请求用户同意新添加的范围。

## 幕后

如果你懂舞蹈，可以跳到[结论](#conclusion)。如果你想学习这些动作，请继续阅读

### 移动 1

> 用户进入`Meetup`应用程序，点击`Google`登录

为了做到这一点，应用程序应该向身份提供者注册自己，通过提供它希望其用户在 [Move 4](#m4) 中看到的最少名称和身份提供者用于交流结果的 url。

这是[客户注册](https://openid.net/specs/openid-connect-registration-1_0.html)。作为注册的结果，现在这个应用程序获得了一个 Id 和一个密码。

### 移动 2

> `Meetup` app 然后要求`Google`认证用户并分享用户的`basic profile`

现在，应用程序使用其 Id 和注册的 Url 形成一个请求，并使用三个流(基本、隐式或混合)之一的`response_type`请求一个`scope`例如基本配置文件，以接收用户的数据

这是一个[认证请求](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)。

### 移动 3

> `Google`然后要求`user`登录

此时，身份提供者检查客户端的有效性(id 和注册的 url)，并要求用户登录。

这是一个身份提供者在做身份的事情。

### 移动 4

> `Google`现在询问`User`他们是否可以与`Meetup`分享他们的`basic profile`

身份认证成功后，身份提供者会向用户显示同意屏幕，描述
应用程序需要什么数据以及为什么需要。

这是得到用户的同意

> 用户如何获得认证或同意超出了 OpenID Connect 标准的范围。

### 移动 5

> `Google`现在与`Meetup`共享一个可以解锁`User's` **同意**数据的临时密钥

身份认证成功并获得用户同意后，身份提供商必须将其与应用程序共享。如何将这些信息共享给应用程序取决于应用程序在[移动 1](#m1) 中提供的`response_type`值

任何流程(基本的、隐式的、混合的)的最终结果都是生成一个或多个`access_token` `refresh_token`和`id_token`。

这是来自基本和混合流的[令牌端点](https://openid.net/specs/openid-connect-core-1_0.html#TokenEndpoint)或隐式流的[授权端点](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)的[成功令牌响应](https://openid.net/specs/openid-connect-core-1_0.html#TokenResponse)。

#### 选择流量

[![](../Images/031b5ea161d2ad9ab321000dea71a134.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ItLK3-hF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kv19qcqfwahq3q8nwmpy.png)

### 移动 6

> `Meetup`使用密钥获取`Basic profile`数据。

现在应用程序可以使用这个短暂的`access_token`来获取用户信息。

这是[用户信息端点](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo)

### 我们需要问的问题，

[![](../Images/95b0b8d9757e30a444eaec52afc83c9a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PfBwGL1C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u60yc2chlby6tsiphflz.png)

因此身份提供者可以验证应用程序和用户，但是**应用程序如何知道**

*   确实是`The Identity Provider`对用户进行了认证和响应？
*   自用户上次被认证以来有多长时间了？鉴于 SSO 带来的所有便利？
*   用户使用哪种安全级别(1FA 或 2FA)进行身份验证？鉴于应用程序处理的当然是钱，我们需要有铁一般的安全性，不是吗？

### 答案是`id_token`

这就是 OpenID Connect 与 OAuth2 的不同之处，它在 OAuth2 标准之上提供了一个身份层。它向应用程序提供关于认证用户的反馈。

[![](../Images/ea2d38e78188b5518196f5af7e4f1bc1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ickk7tOl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/75gty0dmvj8zyafmb1kj.png)

你可以把 [id_token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) 想象成用户的数字安全身份证。它是一个由`Identity Provider's`私钥签名的 [JWT](https://jwt.io) 令牌。公钥通过[密钥端点](https://tools.ietf.org/html/rfc7517)共享

### 结论

通过抽象出用户的身份验证和授权数据，我们可以通过建立用户配置文件，在如何以及何时收集数据方面实现灵活性。身份提供者可以定义范围，这使得应用程序不仅可以获取用户数据，还可以通过范围管理写入用户数据。

该标准为您提供了足够的信任和信心，让您相信应用程序、用户和身份提供商之间的数据交换是安全和透明的。