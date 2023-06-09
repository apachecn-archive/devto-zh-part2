# 关于明文登录详细信息

> 原文：<https://dev.to/oneearedmusic/on-plaintext-login-details-36p>

*本帖原写于 2017 年 1 月 17 日。我正在将我的个人博客上的所有帖子转移到开发人员的*

最近，我注册了一个新的科技女性在线社区。这是“走出去”目标的一部分。我经历了用我的电子邮件注册的标准过程，使用 [LastPass](https://www.lastpass.com/) 生成一个新的密码并将条目保存在我的保险库中，等等。半分钟后，当我收到一封包含我所有登录信息的电子邮件时，我非常震惊...*包括明文密码*！

```
"Congratulations on entering the tech community!

The login information for your account is: 
Email: email@email
Password: password

..." 
```

你对此有什么想法？我四处打听了一下——这似乎曾经是惯例。它是旧的，但是仍然可以使用。

我对这个社区做了更多的调查，很明显这个网站不处理任何用户的个人信息。没有个人资料或聊天部分。该网站主要是本地活动和招聘信息的链接。很明显，这不是一个高安全性的冒险，强大的密码保护可能是不必要的。也就是说，我真的认为所有的服务都应该采取一切可能的措施来帮助用户保护和提高他们的网络安全。如果我没有使用密码生成器，而我的唯一密码实际上已经在另一个平台上使用了，那该怎么办？哎呀。

作为替代，看看 Slack 是如何处理新用户的。注册新频道时，您会收到一封类似这样的电子邮件:

```
"Your Slack Account Information

Hi Erika, 

Welcome to Slack! You've joined the team teamName. In case it's handy,
here's a copy of your account details:

Team Name: teamName
Team Domain: http://teamURL
Sign-in Email Address: email@email

..." 
```

这与你不需要密码就能获得所有信息的目的是一样的。我喜欢它不假设我的电子邮件收件箱绝对安全，同时仍然提供有用的信息，我可以存档。

我联系了新社区的帮助台，对他们的回应非常满意。他们雇佣的公司说包含密码是标准的，但是社区同意为了额外的安全应该删除它，所以它将被逐步淘汰。

## 你如何看待在注册时给用户发电子邮件告知他们的账户信息？还需要吗？应该包括密码吗？