# 一个不在你的应用中使用 OAuth only 账户的好理由

> 原文：<https://dev.to/rhymes/a-good-reason-not-to-use-oauth-only-accounts-in-your-apps-3on1>

或者至少，不要使用脸书认证。

正如你可能知道的，脸书刚刚披露了至少 5000 万账户的数据泄露。

在所有的信息中，有一点引起了我的注意:

> ![rat king profile image](img/e6d5bbcc8e59e281ed0e5af2a60fa52a.png)鼠王@ mikeisaac![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)Via guy Rosen——这种黑客手段比人们想象的还要糟糕。黑客给了你完全访问 facebook 账户的权限，这意味着你可以访问任何其他使用脸书登录的连接应用。2018 年 9 月 28 日 21 点 16 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1045784383536300033)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1045784383536300033)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1045784383536300033)

> ![rat king profile image](img/e6d5bbcc8e59e281ed0e5af2a60fa52a.png)鼠王@ mikeisaac![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)Facebook“根本不知道人们访问了哪些信息”的观点是不真诚的。完全账户权限意味着他们可以做你在脸书做的任何事情。21:23PM-28 Sep 2018[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1045786217713786880)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1045786217713786880)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1045786217713786880)

这让我想起了[@迈克尔](https://dev.to/michael)的这篇文章:

[![michael](img/09f234b674116c14fcaff7f5a364677a.png)](/michael) [## 注册或登录应用程序时，您更喜欢 OAuth(带社交媒体帐户的身份验证)还是电子邮件注册/登录？

### 李名炀🍕Sep 19 ' 18 分钟读取

#discuss](/michael/when-signing-up-or-logging-into-an-app-do-you-prefer-oauth-authentication-wsocial-media-account-or-email-sign-upsign-in-23p6)

使用 OAuth 授权登录你的用户是有好处的，不幸的是，这意味着如果这样的帐户被攻破，所有链接到它的应用都是易受攻击的。

虽然我已经有一段时间没有看到脸书唯一的应用程序/网站了，但如果你不是真的信任登录提供商(现在谁信任脸书？)，请为您的用户身份验证提供替代路径。

ps。脸书花了三天才向公众披露此事