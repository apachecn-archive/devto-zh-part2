# 在 Crystal 中实现一次性密码

> 原文：<https://dev.to/philnash/implementing-one-time-passwords-in-crystal-5fo3>

Crystal 仍然是一门年轻的语言，还没有很多可用的库。对一些人来说，这可能令人不快，但对其他人来说，这是一个学习语言的机会，并为那些也开始使用它的人提供有用的工具。

随着时间的推移，我已经做了[次讲座](https://dev.to/speaking/history/)，其中一些是关于[双因素认证](https://www.youtube.com/watch?v=WipxMQjssRE)。不久前，我抓住机会提高自己的知识，在 Crystal 中实现了[一次性密码算法](https://en.wikipedia.org/wiki/HMAC-based_One-time_Password_algorithm)。

这导致我在 Crystal 中为一次性密码构建了 [CrOTP 库。](https://github.com/philnash/crotp)

如果想查看代码，大部分重要的实现都可以在 [`CrOTP::OTP`模块](https://github.com/philnash/crotp/blob/master/src/crotp/otp.cr)中找到。 [`CrOTP::HOTP`](https://github.com/philnash/crotp/blob/master/src/crotp/hotp.cr) 和 [`CrOTP::TOTP`](https://github.com/philnash/crotp/blob/master/src/crotp/totp.cr) 类负责处理计数器的不同方式。

## 使用 CrOTP

要在您自己的 Crystal 项目中使用 CrOTP 库，您需要将它添加到您的`shard.yml`文件中的依赖项中。

```
dependencies:
  crotp:
    github: philnash/crotp 
```

Enter fullscreen mode Exit fullscreen mode

然后用`shards install`安装依赖项。

现在你可以在你的应用程序中使用它。一次性密码在 web 上的大多数使用是基于时间的令牌，用于双因素身份验证。以下是如何生成和验证基于时间的 OTP。

### 入门

首先需要库并生成一个随机字符串作为密码。

```
require "crotp"

secret = Random.new.hex(16) 
```

Enter fullscreen mode Exit fullscreen mode

然后用这个秘密创建一个`CrOTP::TOTP`类的实例。

```
totp = CrOTP::TOTP.new(secret) 
```

Enter fullscreen mode Exit fullscreen mode

### 分享秘密

你可以生成一个 URI，并与认证应用共享，如 [Authy](https://authy.com/) 或谷歌认证。为此，使用您的应用程序名称作为发布者，使用用户帐户的用户名作为用户来调用`authenticator_uri`。

```
totp.authenticator_uri(issuer: "Test app", user: "philnash@example.com") 
```

Enter fullscreen mode Exit fullscreen mode

将生成的 URI 转换成二维码，用户可以扫描它，将账户添加到他们的应用程序中。

### 生成并验证

现在，您可以使用`totp`对象生成一个新的一次性密码，该密码将在当前的 30 秒内有效。

```
password = totp.generate 
```

Enter fullscreen mode Exit fullscreen mode

要验证代码，可以使用同一个对象，调用`verify`来代替。

```
totp.verify(password) 
```

Enter fullscreen mode Exit fullscreen mode

你也可以允许一个漂移，给用户更多的时间来输入他们的代码。漂移是令牌生成后可以经过的周期数(默认情况下周期为 30 秒)。

```
totp.verify(password, at: Time.now, allowed_drift: 2) 
```

Enter fullscreen mode Exit fullscreen mode

项目回购中还有[个例子。](https://github.com/philnash/crotp/blob/master/example/crotp.cr)

## 还有更多工作要做

[CrOTP](https://github.com/philnash/crotp) 负责生成和验证一次性密码的基本工作。当然，当我有时间的时候，在项目上还有更多[要做。](https://github.com/philnash/crotp#todo)

我很想知道这个项目对您是否有用，或者您是否有兴趣帮助实现缺失的功能。请通过 Twitter 上的 [@philnash](https://twitter.com/philnash) 或下面的评论告诉我。