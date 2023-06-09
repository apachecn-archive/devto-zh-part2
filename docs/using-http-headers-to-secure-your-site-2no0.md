# 使用 HTTP 头来保护您的站点

> 原文：<https://dev.to/heroku/using-http-headers-to-secure-your-site-2no0>

Mozilla 的 Observatory 通过教导开发者、系统管理员和安全专家如何安全地配置他们的站点来帮助网站。

让我们来看看 Observatory 给一个相当简单的[静态构建包](https://github.com/heroku/heroku-buildpack-static)应用，【https://2017.keeprubyweird.com】T2 的分数。

## [考试成绩](https://observatory.mozilla.org/analyze.html?host=2017.keeprubyweird.com)

| 试验 | 及格 | 得分 | 说明 |
| --- | --- | --- | --- |
| [内容安全策略](https://infosec.mozilla.org/Security/Guidelines/Web_Security#content-security-policy) | 一千 | -25 | 内容安全策略(CSP)头未实现 |
| [饼干](https://infosec.mozilla.org/Security/Guidelines/Web_Security#cookies) | ― | Zero | 未检测到 cookies |
| [跨产地资源共享](https://infosec.mozilla.org/Security/Guidelines/Web_Security#cross-origin-resource-sharing) | ✔ | Zero | 内容通过跨源资源共享(CORS)文件或标题不可见 |
| [HTTP 公钥锁定](https://infosec.mozilla.org/Security/Guidelines/Web_Security#http-public-key-pinning) | ― | Zero | 未实现 HTTP 公钥锁定(HPKP)标头(可选) |
| [HTTP 严格的传输安全](https://infosec.mozilla.org/Security/Guidelines/Web_Security#http-strict-transport-security) | 一千 | -20 | HTTP 严格传输安全(HSTS)标头未实现 |
| [重定向](https://infosec.mozilla.org/Security/Guidelines/Web_Security#http-redirections) | ✔ | Zero | 初始重定向到同一主机上的 https，最终目标是 https |
| [推荐策略](https://infosec.mozilla.org/Security/Guidelines/Web_Security#referrer-policy) | ― | Zero | 引用者-策略头未实现(可选) |
| [子资源完整性](https://infosec.mozilla.org/Security/Guidelines/Web_Security#subresource-integrity) | ― | Zero | 未实现子资源完整性(SRI ),但所有脚本都是从相似的来源加载的 |
| [X-内容-类型-选项](https://infosec.mozilla.org/Security/Guidelines/Web_Security#x-content-type-options) | 一千 | -5 | X-Content-Type-Options 标头未实现 |
| [X 帧选项](https://infosec.mozilla.org/Security/Guidelines/Web_Security#x-frame-options) | 一千 | -20 | X-Frame-Options (XFO)标题未实现 |
| [X-XSS-保护](https://infosec.mozilla.org/Security/Guidelines/Web_Security#x-xss-protection) | 一千 | -10 | x-XSS-保护标题未实现 |

即使我们使用 Heroku 的[自动化证书管理](https://devcenter.heroku.com/articles/automated-certificate-management)来轻松地为我们的域获得 SSL 证书，我们的总得分也是 F，20/100。我们将遍历每个失败的测试，了解失败的原因，并尝试修复它们。

### 内容安全策略(CSP)

这里的失败是“CSP header not implemented”，当我们查看链接的安全指南时，我们看到 CSP 使我们能够控制我们站点上引用的脚本和资源可以从哪里加载。对于 Keep Ruby 怪异，这意味着字体，几个外部图像源，和几个分析源。

CSP 为我们提供了几个层次的严格性:

*   `default-src <source>`、`script-src <source>`、`object-src <source>`等。这些限制了各种资源的来源。
    *   将指定类型的资源或所有资源仅限于 HTTPS
    *   将资源限制到此域。可以为同一个`*-src`指令提供多个这样的源。
    *   `'self'`表示资源只能从当前主机加载，对于`<script src="/index.js">`这样的相对资源有用。
    *   请参见 MDN 上的 [CSP: default-src](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) 了解此处的完整选项。
*   默认情况下，`Content-Security-Policy`头不允许`<script>`标签带有内嵌代码。那些用`src`代替的是允许的。这可以通过添加`'unsafe-inline'`来禁用，这会降低我们网站的安全性。您还可以指定`nonce` s 或 SHA sums 的内容来允许这些脚本执行。
*   `frame-ancestors 'none'`防止你的网站被载入 iframe 并被用于[点击劫持攻击](https://www.owasp.org/index.php/Clickjacking)。如果您确实需要在 iframe 中显示您的站点，您可以改为指定 URL。

出于我们的目的，我们将希望能够使用自托管资源，来自 Twitter 和 AWS 的图像，来自 Google 和 Twitter analytics 的脚本，以及来自 Google Fonts 的样式表和字体条目。我们还需要*在一些指令中重新定义* `'self'`，因为除非选项没有被指定，否则它们不会回到缺省值。例如，我们还没有指定 [`object-src`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) ，所以它依赖于`'self'`的`default-src`值。

```
Content-Security-Policy: default-src 'self';
                         script-src https://static.ads-twitter.com https://www.google-analytics.com;
                         img-src 'self' https://s3.amazonaws.com https://twitter.com https://pbs.twimg.com;
                         font-src 'self' https://fonts.gstatic.com;
                         style-src 'self' https://fonts.googleapis.com;
                         frame-ancestors 'none'; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将它添加到我们的 static.json 中，作为所有路径的头集合的一部分:

```
#  ...  "headers":  {  "/**":  {  "Content-Security-Policy":  "default-src 'self'; script-src https://static.ads-twitter.com https://www.google-analytics.com; img-src 'self' https://s3.amazonaws.com https://twitter.com https://pbs.twimg.com; font-src 'self' https://fonts.gstatic.com; style-src 'self' https://fonts.googleapis.com; frame-ancestors 'none';"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

当我们添加或删除外部资源时，我们需要更新此集合，否则我们的用户将在浏览器的控制台中看到错误，并且资源将不可用。

### HTTP 严格的运输安全(HSTS)

我们测试失败的原因基本相同:“HTTP 严格传输安全(HSTS)头没有实现”。

HSTS 告诉一个浏览器，我们的网站只能在 HTTPS 浏览。查看 HSTS 安全指南，我们看到 HSTS 提供了几个非排他性标志:

*   `max-age=<seconds>`。用户代理将重定向到 HTTPS 的时间(秒)。这告诉一个浏览器“一旦你看到这个，假设这个域的所有请求都将通过 HTTPS 这么长时间。”Mozilla 推荐 2 年，即`63072000`秒。此标志是必需的。
*   `includeSubDomains`。用户代理是否应该升级子域上的请求。除非你有理由不在所有子域名上使用 SSL，否则你可能会希望这样做。即使你目前没有子域，我也推荐它。
*   `preload`。如果你有这个标志，并且在 [Chrome HSTS 预加载列表](https://hstspreload.org/)上注册了你的域名，浏览器在强制 HTTPS 请求之前甚至不需要看到这个标题。这是有用的，但选择加入，因为你确实需要通过在预加载列表中查找它来注册自己的域，选中一些框，然后提交。这很简单，我们就在这里做。

```
#  ...  "headers":  {  "/**":  {  "Strict-Transport-Security":  "max-age=63072000; includeSubDomains; preload"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### X-内容-类型-选项

这个头告诉浏览器，如果服务器指示的脚本和样式表的 MIME 类型不正确，就不要加载它们。穿上它是件好事。

```
#  ...  "headers":  {  "/**":  {  "X-Content-Type-Options":  "nosniff"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### X-帧-选项

这个头防止你的站点被加载到 iframe 中。它有助于防止[“点击劫持”攻击](https://www.owasp.org/index.php/Clickjacking)。这与`frame-ancestors 'none'`在内容安全策略中提供的保护相同，但增加了对旧浏览器的支持。如果您确实需要在网站的另一个页面上的 iframe 中显示您的网站，您可以使用`SAMEORIGIN`选项。

```
#  ...  "headers":  {  "/**":  {  "X-Frame-Options":  "DENY"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### X-XSS-保护

该报头可防止[跨站点脚本(XSS)攻击](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))。它提供了与内容安全策略类似的保护，但也保护了较旧的浏览器。

```
#  ...  "headers":  {  "/**":  {  "X-XSS-Protection":  "1; mode=block"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 把所有的东西放在一起

将这个 header 块添加到我们的 static.json 中会将我们在 Observatory 上的分数从 F 增加到 A。

```
"headers":  {  "/**":  {  "Content-Security-Policy":  "default-src 'self'; script-src https://static.ads-twitter.com https://www.google-analytics.com 'sha256-q2sY7jlDS4SrxBg6oq/NBYk9XVSwDsterXWpH99SAn0='; img-src 'self' https://s3.amazonaws.com https://twitter.com https://pbs.twimg.com; font-src 'self' https://fonts.gstatic.com; style-src 'self' https://fonts.googleapis.com; frame-ancestors 'none';",  "Strict-Transport-Security":  "max-age=63072000; includeSubDomains; preload",  "X-Content-Type-Options":  "nosniff",  "X-Frame-Options":  "DENY",  "X-XSS-Protection":  "1; mode=block"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

当我们加载浏览器时，一切看起来都没问题！不幸的是，我们错过了一件事，我们可以在控制台上看到。

> 拒绝执行内联脚本，因为它违反了以下内容安全策略指令:“script-src[https://static.ads-twitter.com](https://static.ads-twitter.com)【https://www.google-analytics.com】T2”。' unsafe-inline '关键字、哈希(' sha 256-q 2 sy 7 jlds 4 srxbg 6 OQ/nbyk 9 xvswdsterxwph 99 San 0 = ')或 nonce ('nonce-... ')是启用内联执行所必需的。

即使我们将[https://www.google-analytics.com](https://www.google-analytics.com)添加到我们的脚本-src 中，因为它是在内联脚本中加载的，所以我们需要允许它显式运行。该错误消息很友好，为我们提供了几个选项:“要么是' unsafe-inline '关键字、一个哈希(' sha 256-q 2 sy 7 jlds 4 srxbg 6 OQ/nbyk 9 xvswdsterxwph 99 San 0 = ')，要么是一个 nonce ('nonce-...)是启用内联执行所必需的。"

听起来，嗯，不安全，所以我们跳过它。nonce 是允许内联脚本运行的一次性数字。随机数可以变得安全，但是我们讨论的静态页面超出了这里的范围。错误消息中提供的哈希是内联代码块内容的实际 sha-256 总和，比其他选项更安全。它将阻止攻击者改变谷歌分析内联脚本的内容，这使得它比无保护的内联脚本更安全。就像改变外部依赖关系一样，如果我们改变了脚本标签，我们也需要改变 sha-256 和。

```
"Content-Security-Policy": "default-src 'self'; script-src https://static.ads-twitter.com https://www.google-analytics.com 'sha256-q2sY7jlDS4SrxBg6oq/NBYk9XVSwDsterXWpH99SAn0='; img-src 'self' https://s3.amazonaws.com https://twitter.com https://pbs.twimg.com; font-src 'self' https://fonts.gstatic.com; style-src 'self' https://fonts.googleapis.com; frame-ancestors 'none';" 
```

Enter fullscreen mode Exit fullscreen mode

我们已经添加了 SHA sum，现在 Google Analytics 已经设置好了！

## 结果

| 试验 | 及格 | 得分 | 说明 |
| --- | --- | --- | --- |
| [内容安全策略](https://wiki.mozilla.org/Security/Guidelines/Web_Security#Content_Security_Policy) | ✔ | +5 | 在没有`'unsafe-inline'`或`'unsafe-eval'`的情况下实施内容安全策略(CSP) |
| [饼干](https://wiki.mozilla.org/Security/Guidelines/Web_Security#Cookies) | ― | Zero | 未检测到 cookies |
| [跨产地资源共享](https://wiki.mozilla.org/Security/Guidelines/Web_Security#Cross-origin_Resource_Sharing) | ✔ | Zero | 内容通过跨源资源共享(CORS)文件或标题不可见 |
| [HTTP 公钥锁定](https://wiki.mozilla.org/Security/Guidelines/Web_Security#HTTP_Public_Key_Pinning) | ― | Zero | 未实现 HTTP 公钥锁定(HPKP)标头(可选) |
| [HTTP 严格的传输安全](https://wiki.mozilla.org/Security/Guidelines/Web_Security#HTTP_Strict_Transport_Security) | ✔ | Zero | HTTP 严格传输安全(HSTS)标头设置为最少六个月(15768000) |
| [重定向](https://wiki.mozilla.org/Security/Guidelines/Web_Security#HTTP_Redirections) | ✔ | Zero | 初始重定向到同一主机上的 https，最终目标是 https |
| [推荐策略](https://wiki.mozilla.org/Security/Guidelines/Web_Security#Referrer_Policy) | ✔ | Zero | 引用者-策略头未实现(可选) |
| [子资源完整性](https://wiki.mozilla.org/Security/Guidelines/Web_Security#Subresource_Integrity) | ― | Zero | 未实现子资源完整性(SRI ),但所有脚本都是从相似的来源加载的 |
| [X-内容-类型-选项](https://wiki.mozilla.org/Security/Guidelines/Web_Security#X-Content-Type-Options) | ✔ | Zero | X-Content-Type-Options 标题设置为`"nosniff"` |
| [X 帧选项](https://wiki.mozilla.org/Security/Guidelines/Web_Security#X-Frame-Options) | ✔ | +5 | X-Frame-Options (XFO)通过 CSP `frame-ancestors`指令实施 |
| [X-XSS-保护](https://wiki.mozilla.org/Security/Guidelines/Web_Security#X-XSS-Protection) | ✔ | Zero | x-XSS-保护头设置为`"1; mode=block"` |

我们达到了 A+！对于一个小时的工作来说还不错，我们的用户现在在访问我们的网站时更安全了。

## 额外学分

我们现在在最后冲刺阶段。我们可以做一些可选的事情来进一步加强安全性和隐私性。

### 推荐人策略

浏览器包含一个`Referrer`标题，标识用户在访问新页面时来自哪里。它有助于追踪用户来自哪里，但这也有一些隐私问题。`Referrer-Policy`头控制何时提供信息以及提供多少信息。

*   `no-referrer`。告诉浏览器永远不要发送`Referer`头。
*   `same-origin`。发送推荐人，但仅限于站点内部的请求(例如/security-in-the-static-build pack =>/posts)
*   `strict-origin`。向所有来源发送参考信息，但只发送没有路径的 URL(例如[https://example.com/](https://example.com/)
*   `strict-origin-when-cross-origin`。发送相同来源的完整参考信息，但只发送外部来源的 URL 无路径。

`no-referrer`可用作浏览器的后备选项，因为这些选项中有许多尚未实现。

```
Referrer-Policy: no-referrer, strict-origin-when-cross-origin 
```

Enter fullscreen mode Exit fullscreen mode

### 更？

Mozilla Observatory 也测试了 [Cookies](https://wiki.mozilla.org/Security/Guidelines/Web_Security#Cookies) 和[子资源完整性](https://wiki.mozilla.org/Security/Guidelines/Web_Security#Subresource_Integrity)，但是在我们做出改变后，它对 Keep Ruby 怪异网站感到满意，所以这些留给读者作为练习。

## 最终结果

这是这一变化的最终结果，不包括 HSTS 的选择加入“预加载”指令，这是我们对所有静态 buildpack 应用程序的建议。

```
{  "headers":  {  "/**":  {  "Content-Security-Policy":  "default-src 'self'; script-src https://static.ads-twitter.com https://www.google-analytics.com 'sha256-q2sY7jlDS4SrxBg6oq/NBYk9XVSwDsterXWpH99SAn0='; img-src 'self' https://s3.amazonaws.com https://twitter.com https://pbs.twimg.com; font-src 'self' https://fonts.gstatic.com; style-src 'self' https://fonts.googleapis.com; frame-ancestors 'none';",  "Referrer-Policy":  "no-referrer, strict-origin-when-cross-origin",  "Strict-Transport-Security":  "max-age=63072000; includeSubDomains",  "X-Content-Type-Options":  "nosniff",  "X-Frame-Options":  "DENY",  "X-XSS-Protection":  "1; mode=block"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode