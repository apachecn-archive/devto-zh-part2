# 宣布 ppassportct 主动式网络安全

> 原文：<https://dev.to/oktadev/announcing-passprotect---proactive-web-security-1d5c>

如果你正在阅读这篇文章，你可能关心网络安全。你可能使用密码管理器来管理你的密码，你可能已经为你的所有服务设置了[多重认证](https://2fanotifier.org/)，你可能已经订阅了[？](https://haveibeenpwned.com/)因此，当您的某个登录帐户出现数据泄露时，您会收到警报。

但是你不是大多数人。

大多数网络用户与过去几年网络安全领域取得的令人难以置信的进步完全脱节。大多数网络用户在他们的凭证因数据泄露而泄露时不会得到通知，更糟糕的是，大多数凭证*被*泄露的用户从不重置他们的密码。

如果用户每天访问的网站可以在用户使用的凭据不安全时自动通知用户，那岂不是很棒？这将使他们有机会在攻击者利用他们之前更改他们不安全的密码。这将为他们提供将个人信息安全掌握在自己手中所需的知识。

这就是我们新的开发者库 [PassProtect](https://github.com/oktasecuritylabs/passprotect-js) 所支持的。

PassProtect 不会成为入侵的受害者，而是将最普通的互联网用户转变为与你我同等的数据安全专家。

PassProtect 的工作方式很简单，通过在您的网页中包含一个 JavaScript 标签，如果您的用户在您的网站上输入的凭据不安全，他们将立即收到通知。

```
<html>
  <head>
    <!-- ... -->
  </head>
  <body>
    <!-- ... -->
    <script src="https://cdn.passprotect.io/passprotect.min.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

用户看到了什么？信息通知，为他们提供做出正确选择所需的信息:

[![PassProtect Demo](img/5f95ef9020e7ba5d827c9f30fa48af5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y3lhxvX7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets/blog/passprotect/passprotect-demo-33f7bbb6d8063f027318d16e4890d8285ca52950f8c393126c3ee80105dbc8e2.gif)

PassProtect 专为临时用户设计，提供简单但信息丰富的通知。为了避免令人讨厌，PassProtect 使用智能缓存来确保通知不会以任何损害用户体验的方式在单个会话中重复。

此外，PassProtect 借用了神话般的[我被 pwn 了吗？](https://haveibeenpwned.com/)服务，互联网上最大的泄密凭证数据库(由我们的朋友[特洛伊·亨特](https://www.troyhunt.com/)创建)。

由于 PassProtect 处理的数据非常敏感(用户密码)，我们确保 PassProtect 不会通过网络存储、收集或发送任何密码数据。相反，PassProtect 依赖于[k-anonymous](https://blog.cloudflare.com/validating-leaked-passwords-with-k-anonymity/)(由我们在 Cloudflare 的朋友创建)，这恰好是验证远程数据库中存在密码的最佳方式，而无需通过网络发送该密码(或该密码的完整哈希)。=)

不相信我？检查出[的来源](https://github.com/oktasecuritylabs/passprotect-js)！

这听起来很棒，对吧！？唯一的问题是，只有每个开发者都在他们的网站中嵌入 PassProtect，我们大幅提高临时网络用户安全性的目标才能实现。这就是为什么我也继续为 PassProtect 构建了一个 Chrome 扩展。火狐支持将很快推出。

通过这种方式，想要直接利用 PassProtect 的网络用户可以这样做——通过这种方式,*他们访问的每个*网站将立即自动继承 PassProtect 的功能！

没有什么是真正安全的。总有一天，所有系统都会受到某种形式的漏洞的影响。安全永远是攻击者和防御者之间的猫捉老鼠游戏，要想赢，你需要抱最好的希望，做最坏的准备。

我们真诚地希望 PassProtect 能够帮助个人用户在必要时重置他们的凭据，并负责他们的个人数据安全，从而将负面影响(数据泄露)变得更加积极。

如果你想为你的网站获得 PassProtect，请查看我们的 [GitHub repo](https://github.com/oktasecuritylabs/passprotect-js) (包含更多信息)。如果你想在你的浏览器中使用 PassProtect，请查看我们的 [Chrome 扩展](https://chrome.google.com/webstore/detail/passprotect/cpimldclklpfifolmdnicjnfbjdepjnf)。

在外面注意安全！<3

如果你有机会和 [PassProtect](https://www.passprotect.io) 一起玩，请告诉我你的想法！请在下面留下你的评论，或者给我[发一封电子邮件](//mailto:randall.degges@okta.com)。