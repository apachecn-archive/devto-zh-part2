# 脸书的安全漏洞是“预期功能”

> 原文：<https://dev.to/lethargilistic/this-facebook-security-breach-was-intended-behavior-1o2i>

随着[脸书(超过)5000 万账户](https://www.theguardian.com/technology/2018/sep/28/facebook-50-million-user-accounts-security-berach)被攻破，我们开始[得到一些解释](https://newsroom.fb.com/news/2018/09/security-update/)，它们令人毛骨悚然。没有一个团队是完美的，但它提醒我们在安全分析和审计中遗漏一些东西的后果。公司外包这些困难劳动力的方式之一是[向指出漏洞的白帽安全研究人员提供漏洞奖金](https://www.facebook.com/whitehat/)。脸书目前的麻烦让推特用户 [@codepaintsleep](https://twitter.com/codepaintsleep) 想起了他们朋友与那个程序的互动。

> ![♎ profile image](../Images/0889a51119a453fe88ca5267009e4821.png)T3】♎@ codepaint sleep![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)你知道有些“忘记密码”的链接怎么点一下就能登录吗？2016 年 12 月，这适用于发送给来自脸书的特定个人的所有链接。我的一个朋友通过转发链接看到的，我确认了，他举报了。他们甚至没有给他昆虫赏金。[twitter.com/me_irl/status/…](https://t.co/l1dHjJzwlZ)2018 年 9 月 29 日下午 12:09政府人 @me_irllmao。显然，脸书的“查看身份”功能(允许你查看自己的个人资料，就像你选择的其他用户看到的一样，并应用隐私限制)的实现涉及到将其他用户的私人访问令牌加载到你的会话中？？？https://t.co/9UJisodNkv[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1046009091016052736)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1046009091016052736)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1046009091016052736)

2016 年 12 月，脸书将在特定情况下向人们发送自动登录电子邮件。如果这封邮件被转发给其他人，他们将能够点击该链接，并获得您的脸书帐户的完全访问权限。也许这种情况很少见，但如果真的发生了，这就是对某人隐私的严重侵犯。@codepaintsleep 的朋友举报漏洞绝对是做对了，他们一开始甚至不是为了赏金才这么做的。

然而，我不认为最愤世嫉俗的脸书评论家会预测到脸书不授予奖金的原因——他们说，通过转发给自己的电子邮件，让别人访问你的账户是“预期的功能”它的功能如此强大，以至于脸书在一位好心人报告后的 20 分钟内就修补并关闭了这个漏洞。

> ![unknown tweet media content](../Images/9cec7a43ad86db3f41f8e1101c8217da.png)![♎ profile image](../Images/0889a51119a453fe88ca5267009e4821.png)T6】♎@ codepaint sleep![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)我有 facebook 的案例号，如果我关注的人能用它做些有用的事情。也是我们当时的谈话，尽管除了我的确认之外，没什么有趣的。2018 年 9 月 29 日下午 13:22[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1046027352390742016)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1046027352390742016)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1046027352390742016)

显然，这只是一个不付款的借口，也许它不需要比一个数十亿美元的公司更吝啬。但是不支付你的 bug 奖金，尤其是像这样的东西，就违背了这个项目的目的。

虽然脸书为争议一次又一次地道歉，说他们已经从错误的后果中学到了很多东西，但是请记住，他们的商业模式无视你的隐私，这是他们唯一想要的功能。