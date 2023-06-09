# 网络的崩溃。

> 原文：<https://dev.to/shamar/the-meltdown-of-the-web-4p1m>

正如布鲁斯·佩伦斯最近所说的，我“只是一名程序员”。
T3[一个卑微的程序员](https://www.cs.utexas.edu/~EWD/transcriptions/EWD03xx/EWD340.html)。而且是自学成才的。

一个程序员从一群奇怪的人那里学会了如何编程，他们的核心价值观是**好奇心**:T2 黑客。

所以当我看到一个安全漏洞以各种方式影响 T2 数十亿人时，我表现得像一个程序员。我试着修复它...或者把它修好。尽快。

所以一个月前，我写了一篇文章解释网络如何仍然是 DARPA 的武器。

在那里，我描述了互联网和网络的两个危险的开端。

一旦我意识到**大多数安全专家不了解问题**的严重性，我就和[的 Mozilla 开发者](https://mastodon.social/@Shamar/100612439781128912)谈论这件事，他建议[向 Mozilla](https://wandering.shop/@callahad/100621620793416331/embed) 公开一个问题。

因此，我花了[两个小时写了一份详细的 bug 报告](https://bugzilla.mozilla.org/show_bug.cgi?id=1487081)，但是很快就关闭了(没有说火狐用户是否容易受到这样的攻击)，因为

> Bugzilla 不是一个讨论论坛。

在建议的 Lobste.rs 线程上([缓存在这里](https://shamar.github.io/documents/Mozilla-Bug1487081-Attachments/Undetectable_Remote_Arbitrary_Code_Execution_Attacks_through_JavaScript_and_HTTP_headers_trickery__Lobsters.html))，我问 Firefox 用户是否容易受到如此广泛的攻击(几次)而没有得到响应。

反而得到了几个[讽刺的](https://shamar.github.io/documents/Mozilla-Bug1487081-Attachments/Undetectable_Remote_Arbitrary_Code_Execution_Attacks_through_JavaScript_and_HTTP_headers_trickery__Lobsters.html#c_dlubhc)、[居高临下的](https://shamar.github.io/documents/Mozilla-Bug1487081-Attachments/Undetectable_Remote_Arbitrary_Code_Execution_Attacks_through_JavaScript_and_HTTP_headers_trickery__Lobsters.html#c_p4qxvi)、[甚至](https://shamar.github.io/documents/Mozilla-Bug1487081-Attachments/Undetectable_Remote_Arbitrary_Code_Execution_Attacks_through_JavaScript_and_HTTP_headers_trickery__Lobsters.html#c_e4xjgu) - [侮辱的](https://shamar.github.io/documents/Mozilla-Bug1487081-Attachments/Undetectable_Remote_Arbitrary_Code_Execution_Attacks_through_JavaScript_and_HTTP_headers_trickery__Lobsters.html#c_2wvw4r) - [评论](https://shamar.github.io/documents/Mozilla-Bug1487081-Attachments/Undetectable_Remote_Arbitrary_Code_Execution_Attacks_through_JavaScript_and_HTTP_headers_trickery__Lobsters.html#c_0y21ub)。

这么简单的问题还是没有答案。火狐用户容易受到攻击吗？

当[我把同样的问题](https://bugs.chromium.org/p/chromium/issues/detail?id=879381)报告给 Chromium 团队的时候，[不到十分钟就关闭了，同样的语气](https://shamar.github.io/documents/Mozilla-Bug1487081-Attachments/ChromiumBug879381.html):

> 在这里提交一个 bug 并不是改变 web 标准的方法，不管你对它们的感觉如何。

这里值得注意的是，Mozilla 和 Google 都是 WHATWG 的成员，他们制定了我们正在谈论的 T2 生活标准。基本上**跟随**实施的生活水平。

在我看来，这意味着你必须修正实现来修正标准...但是记住，**我只是一个程序员**！

现在，我想我已经非常清楚 JavaScript 带来的广泛攻击了。当被要求这么做时，[我甚至仔细地解释了修理它们有多简单。](https://medium.com/@giacomo_59737/i-compiled-a-detailed-bug-report-for-mozilla-where-i-described-some-of-the-possible-attacks-and-41843f7e84d8)

但是自从

> 这是按设计运行的网站

我想让你看看**网络是为**设计的。

# 许多可能利用的概念验证(绕过公司防火墙)

请在您的 C:\ Windows \ System32 \ drivers \ etc \ hosts 中添加一个临时的行，包含

> 127 . 0 . 0 . 1 local.jsfiddle.net

这模拟了攻击者对 DNS 的控制。

然后用 WHATWG 浏览器试试这个简单的 JSFiddle 。

您可以在第 21 行更改端口号，以测试 PC 上的任何端口。

您可以更改/etc/host 中的 IP 来探测 LAN 上的其他机器。

JSFiddle(虚构的攻击者)刚刚**绕过了你的公司防火墙/代理**。

# 万物皆破。

这只是**中的**一个**还有无数的攻击**你可以这样做。

我可以继续**小时**发明更多的攻击。你也应该可以。

编辑:[`here you can find another exploit`T2】](https://rain-1.github.io/in-browser-localhostdiscovery)

正如错误报告中所解释的，您可以针对特定的人或组。

甚至通过 CDN(从而通过受害者信任的第三方网站)。

然后，您可以从同一个 url 重新加载一个无害的脚本，重写缓存副本并**删除攻击的所有证据**。

这真的只是一个**竞争力**和**幻想**的问题。

我还是不会找一个[酷的名字](http://heartbleed.com/)或者[画布偶](https://meltdownattack.com/)给[“传道”这件事](https://twitter.com/BrucePerens/status/1035617158984654848)。我是程序员，**不是小丑**。

# 怎么才能修好？

正如我在错误报告中解释的那样，**技术解决方案**基本上是

*   **让用户在每个网站的基础上选择加入程序执行**
*   威胁这样的程序有潜在的危险

你可以在这里阅读[的简单回顾和细节。](https://medium.com/@giacomo_59737/i-compiled-a-detailed-bug-report-for-mozilla-where-i-described-some-of-the-possible-attacks-and-41843f7e84d8)

然而，你在这里可以看到的是**网络被破坏的深度**。

这(不仅仅)是关于 JavaScript 的。

这是关于 **[人](https://twitter.com/giacomotesio/status/1035908349903163392)** 的。