# 在 Go 中保护 Cookies

> 原文：<https://dev.to/joncalhoun/securing-cookies-in-go--1fbe>

> *这篇文章最初发表在我的网站上[calhoun . io](https://www.calhoun.io/securing-cookies-in-go/)T3】*

当我刚开始学习围棋的时候，我有过网络开发的经验，但是直接使用 cookies 的经验稍少。我来自 Rails 背景，虽然我必须在 Rails 中读/写 cookies，但实际上我并不需要自己实现所有的安全措施。

你看，Rails 很擅长默认完成大多数事情。你不需要去设置 CSRF 对策，或做任何特殊的加密你的饼干。在较新版本的 Rails 中，这些都是默认完成的。

在围棋中开发就很不一样了。这些都不是默认为您做的，所以当您想开始使用 cookies 时，了解所有这些安全措施、它们为什么存在以及如何将它们整合到您自己的应用程序中是至关重要的。这篇文章旨在帮助你做到这一点。

注意: *这并不是要引发一场关于哪条路线更好的争论/讨论。两者各有千秋，但我想把重点放在保护 cookies 上，而不是 Rails vs Go。*

## 什么是饼干？

在我们开始讨论 cookie 的安全性之前，理解什么是真正的 cookie 是很重要的。就其核心而言，cookies 只是存储在最终用户计算机上的键/值对。因此，您真正需要做的唯一事情就是设置 [http 的名称和值字段。Cookie](https://golang.org/pkg/net/http/#Cookie) 类型，然后调用 [http。SetCookie](https://golang.org/pkg/net/http/#SetCookie) 函数告诉最终用户的浏览器设置该 Cookie。

在代码中，它看起来像这样:

```
func someHandler(w http.ResponseWriter, r *http.Request) {
  c := http.Cookie{
    Name: "theme",
    Value: "dark",
  }
  http.SetCookie(w, &c)
} 
```

Enter fullscreen mode Exit fullscreen mode

**旁注:SetCookie 不返回错误。** *`http.SetCookie`不返回错误，但可能会默默地丢弃一个无效的 cookie。这不是一个很棒的体验，但它就是这样，所以在使用这个函数时一定要记住这一点。*

虽然看起来我们在代码中“设置”了一个 cookie，但实际上我们只是在响应中发回了一个`"Set-Cookie"`头，它定义了我们想要设置的 cookie。我们不在我们的服务器上存储 cookies，而是依靠最终用户的浏览器来创建和存储它们。

我想强调这一点，因为它有非常严重的安全影响。我们无法控制这些数据。最终用户的计算机(最终用户)最终控制着这些数据。

当读取和写入最终用户控制的数据时，我们需要非常小心地对待这些数据。恶意用户可以删除 cookie，编辑存储在其中的数据，或者我们甚至可能遭遇[中间人攻击](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)，当用户将 cookie 发送到我们的 web 服务器时，有人试图窃取 cookie。

## cookie 的潜在问题

根据我的经验，我发现与 cookies 相关的安全问题大致分为五类。下面我们将简要介绍每一种方法，然后本文的其余部分将专门详细讨论每一种方法及其对策。

**1。Cookie 盗窃** -攻击者可以通过各种方式试图窃取 cookie。我们将讨论如何防止/缓解其中的大部分问题，但归根结底，我们无法完全防止物理设备访问。

**2。Cookie 篡改**——不管是有意还是无意，cookie 中的数据都可能被篡改。我们将讨论如何验证存储在 cookie 中的数据确实是我们编写的有效数据。

**3。数据泄露** - Cookies 存储在最终用户的电脑上，所以我们应该注意我们在那里存储了什么数据以防泄露。

**4。跨站点脚本(XSS)**——虽然与 cookie 没有直接关系，但 XSS 攻击在访问 cookie 时会更加强大。我们应该考虑限制脚本在不需要的地方访问我们的 cookies。

**5。跨站点请求伪造(CSRF)** -这些攻击通常依赖于用户使用存储在 cookie 中的会话登录，因此我们将讨论如何防止它们，即使我们以这种方式使用 cookie。

如我之前所述，我们将在本文中逐一解决这些问题，最终您将能够像专家一样锁定您的 cookies。

## 饼干失窃

Cookie 盗窃就像它听起来的那样——有人偷了一个用户的 cookie，通常是为了伪装成那个用户。

Cookies 通常以两种方式之一被盗:

1.  [中间人攻击](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)，或者类似的攻击，攻击者拦截你的网络请求并从中窃取 cookie 数据。
2.  接触到硬件。

当您的网站使用 cookies 时，防止中间人攻击基本上可以归结为始终使用 SSL。通过使用 SSL，其他人根本不可能介入请求，因为他们无法解密数据。

对于那些认为“啊，中间人攻击不太可能…”我强烈建议你去看看 firesheep，这是一个简单的工具，旨在说明窃取通过公共 wifi 发送的未加密 cookies 是多么容易。

如果您想确保这种情况不会发生在您的用户身上，那么就设置 SSL 吧！。 [Caddy Server](https://caddyserver.com/) 让加密变得轻而易举。就用吧。为生产环境设置真的非常简单。例如，您可以用 4 行代码轻松地代理您的 Go 应用程序:

```
calhoun.io {
  gzip
  proxy / localhost:3000
} 
```

Enter fullscreen mode Exit fullscreen mode

Caddy 将自动为您处理所有与 SSL 相关的事情。

通过访问硬件来防止 cookie 盗窃是一个更加棘手的场景。我们不能强迫我们的用户使用安全系统或正确地给他们的电脑设置密码，所以总有一些人会坐在他们的电脑前偷一些饼干然后离开的风险。Cookies 也可能通过病毒被窃取，所以如果用户点击了可疑的附件，我们可能会看到类似的情况。

更具挑战性的是，它更难被发现。如果有人偷了你的手表，当你意识到它不在你的手腕上时，你会注意到的。另一方面，Cookies 可以被复制和经常使用，而没有人意识到。

虽然不是故障保险，但您可以使用一些技术来检测被盗的 cookies。例如，您可以跟踪用户使用什么设备登录，然后标记任何新的硬件，要求他们重新输入密码。你也可以追踪 IP 地址，提醒用户可疑的登录位置。

所有这些解决方案都需要更多的后端工作来跟踪数据，如果您的应用程序处理敏感数据、资金或者越来越受欢迎，这些都是您应该努力的方向。

也就是说，对于大多数应用程序来说，这在第一个版本中可能是多余的，简单地使用 SSL 就足够了。

## Cookie 篡改(又名用户伪造数据)

让我们面对现实吧——有些人是混蛋，有人会试图查看你设置的 cookie 并试图改变它的值。即使只是出于好奇，它也会发生，你需要为此做好准备。

在某些情况下，我们可能不在乎。例如，如果我们让用户定义一个主题偏好，我们可能并不在乎他们是否会改变。当它无效时，我们可以恢复到一个默认的主题，如果他们把它改成一个有效的主题，我们就可以使用这个主题而不会造成任何伤害。

在其他情况下，我们可能会更加关心。编辑会话 cookies 并试图冒充另一个用户比改变你的主题要重要得多，我们显然不希望 Joe 冒充 Sally。

我们将介绍两种检测和防止 cookie 篡改的策略。

#### 1。对数据进行数字签名

对数据进行数字签名是向数据添加“签名”的行为，这样您就可以验证它的真实性。数据不需要对最终用户加密或屏蔽，但我们确实需要向 cookie 中添加足够的数据，以便在用户更改数据时可以检测到。

cookie 的工作方式是通过散列——我们散列数据，然后将数据和数据的散列一起存储在 cookie 中。然后，当用户将 cookie 发送给我们时，我们再次散列数据，并验证它是否与我们创建的原始散列相匹配。

我们不希望用户也创建新的哈希，所以你会经常看到像 HMAC 这样的哈希算法被使用，以便数据可以使用秘密密钥进行哈希处理。这可以防止最终用户编辑数据和数字签名(哈希)。

**注意:** *默认情况下，数字签名数据内置于 [JSON Web 令牌(JWT)](https://jwt.io/) 中，因此您可能已经熟悉了这种方法。*

这可以在 Go 中使用一个像 Gorilla 的 [securecookie](http://www.gorillatoolkit.org/pkg/securecookie) 这样的包来完成，在创建一个`SecureCookie`时，你向它提供一个散列密钥，然后使用那个对象来保护你的 cookie。

```
// It is recommended to use a key with 32 or 64 bytes, but
// this key is less for simplicity.
var hashKey = []byte("very-secret")
var s = securecookie.New(hashKey, nil)

func SetCookieHandler(w http.ResponseWriter, r *http.Request) {
  encoded, err := s.Encode("cookie-name", "cookie-value")
  if err == nil {
    cookie := &http.Cookie{
      Name:  "cookie-name",
      Value: encoded,
      Path:  "/",
    }
    http.SetCookie(w, cookie)
    fmt.Fprintln(w, encoded)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以通过在另一个处理程序中使用相同的 SecureCookie 对象来读取这个 cookie。

```
func ReadCookieHandler(w http.ResponseWriter, r *http.Request) {
  if cookie, err := r.Cookie("cookie-name"); err == nil {
    var value string
    if err = s.Decode("cookie-name", cookie.Value, &value); err == nil {
      fmt.Fprintln(w, value)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**注:** *例题改编自[http://www.gorillatoolkit.org/pkg/securecookie](http://www.gorillatoolkit.org/pkg/securecookie)例题。*

**注:** *这里的数据没有加密，只是编码。我们将在“数据泄漏”一节中讨论如何加密数据。*

对这种模式的一个重要警告是，如果您以这种方式进行身份验证，您需要小心，并遵循类似 JWTs 使用的模式，在这种模式中，您在经过数字签名的数据中添加用户信息和到期日期。您不能只依赖 cookie 的到期日期，因为存储在 cookie 上的日期是没有签名的，用户可以创建一个没有到期日期的新 cookie，并将签名的 cookie 复制到其中，实质上是创建一个让他们永远登录的 cookie。

#### 2。混淆数据

另一个解决方案是掩盖你的数据，让用户无法伪造。例如，而不是像这样存储一个 cookie:

```
// Don't do this
http.Cookie{
  Name: "user_id",
  Value: "123",
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将映射到真实数据的数据存储在数据库中。这通常是通过会话 id 或记忆令牌来完成的，其中我们有一个名为`remember_tokens`的表，然后在其中存储数据，如下所示:

```
remember_token: LAKJFD098afj0jasdf08jad08AJFs9aj2ASfd1
user_id: 123 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将只在 cookie 中存储 remember 令牌，即使用户想要伪造它，他们也不知道要将其更改为什么。只是看起来像胡言乱语。

稍后，当用户访问我们的应用程序时，我们将在数据库中查找记住令牌，并确定他们是以哪个用户的身份登录的。

为了更好地工作，您需要确保您的模糊数据是:

*   映射到用户(或一些其他资源)
*   随意
*   具有显著的熵
*   可以被无效(例如删除/更改存储在数据库中的令牌)

这种方法的一个缺点是，它需要在每个页面请求时查找数据库来验证用户，但这很少被注意到，并且可以通过缓存和其他类似的技术来缓解。与 JWT 相比，这种方法的一个好处是您可以立即使会话无效。

注意: *这是我所知道的最常见的认证策略，尽管 JWT 最近在所有的 JS 框架中变得越来越流行。*

## 数据泄露

数据泄露通常需要另一种攻击媒介——如 cookie 盗窃——才能成为真正的问题，但是谨慎一点总是好的。仅仅因为一个 cookie 被盗并不意味着我们想意外地告诉攻击者用户的密码。

每当在 cookies 中存储数据时，总是尽量减少存储在那里的敏感数据的数量。不要存储像用户密码这样的东西，并且确保任何编码的数据也没有这个。像[这篇文章](https://hackernoon.com/your-node-js-authentication-tutorial-is-wrong-f1a3bf831a46#2491)这样的文章指出了一些例子，开发人员不知不觉地将敏感数据存储在 cookies 或 jwt 中，因为这些数据是 base64 编码的，但实际上任何人都可以解码这些数据。它是编码的，不是加密的。

这是一个相当大的错误，所以如果你担心意外存储敏感的东西，我建议你看看像 Gorilla 的 [securecookie](http://www.gorillatoolkit.org/pkg/securecookie) 这样的包。

之前我们讨论了如何使用它对你的 cookie 进行数字签名，但是`securecookie`也可以用来加密和解密你的 cookie 数据，这样它就不容易被解码和读取。

要启用包的加密，您只需要在创建您的`SecureCookie`实例时传递一个块密钥。

```
var hashKey = []byte("very-secret")
// Add this part for encryption.
var blockKey = []byte("a-lot-secret")
var s = securecookie.New(hashKey, blockKey) 
```

Enter fullscreen mode Exit fullscreen mode

其他一切都与本文数字签名部分的示例相同。

这里有必要提及的是，您仍然不应该在 cookie 中存储任何真正敏感的数据；尤其是像密码这样的东西。加密只是一种技术，在半敏感的东西最终出现在 cookie 中的情况下，帮助保护一些额外的东西。

## 跨站脚本(XSS)

[跨站点脚本](https://en.wikipedia.org/wiki/Cross-site_scripting)，通常被称为 XSS，当有人设法将一些不是你写的 JavaScript 注入到你的站点时就会发生，但由于攻击的工作方式，浏览器不知道这一点，并运行 JavaScript，就好像你的服务器确实提供了代码一样。

一般来说，您应该尽最大努力阻止 XSS，我们不会在这里详细讨论它是什么，但是为了防止它溜走，我建议在不需要的时候禁用对 cookies 的 JavaScript 访问。如果你需要的话，你可以随时启用它，所以不要让它成为你易受攻击的借口。

在围棋中做到这一点很简单。您只需在创建的任何 cookies 中将`HttpOnly`字段设置为 true。

```
cookie := http.Cookie{
  // true means no scripts, http requests only. This has
  // nothing to do with https vs http
  HttpOnly: true,
} 
```

Enter fullscreen mode Exit fullscreen mode

## CSRF(跨站点请求伪造)

当用户访问一个不属于你的站点，但是这个站点有一个提交给你的 web 应用程序的表单时，就会发生 CSRF。因为最终用户提交表单，而这不是通过脚本完成的，所以浏览器将此视为用户发起的操作，并随表单提交一起传递 cookies。

乍一看，这似乎并不太糟糕，但是当外部站点开始发送用户不想要的数据时，会发生什么呢？例如，badsite.com 可能有一个表单，提交一个将 100 美元转移到他们在 chase.com 的银行帐户的请求，希望您登录到那里的银行帐户，这可能会导致资金在最终用户不愿意的情况下被转移。

Cookies 对此没有直接责任，但是如果你使用 cookies 进行身份验证，你需要使用 Gorilla 的 [csrf](http://www.gorillatoolkit.org/pkg/csrf) 这样的包来防止这种情况。

这个包的工作原理是为你提供一个 CSRF 令牌，你可以将它插入到每个 web 表单中，每当提交一个没有令牌的表单时,`csrf`包的中间件将拒绝该表单，使外部网站无法欺骗用户提交表单。

有关什么是 CSRF 的更多信息，请参见以下内容:

*   [https://www . owasp . org/index . PHP/Cross-Site _ Request _ Forgery _(CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))
*   [https://en.wikipedia.org/wiki/Cross-site_request_forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery)

## 在不需要的地方限制 cookie 的访问

我们要讨论的最后一件事与特定的攻击无关，但更像是我在使用 cookies 时建议的指导原则——尽可能限制访问，只在需要时提供访问。

我们在讨论 XSS 的时候简单地提到了这一点，但是总的想法是你应该尽可能地限制对你的 cookies 的访问。例如，如果您的 web 应用程序不使用子域，您没有理由让所有子域访问您的 cookies。这是 cookies 的默认设置，因此您实际上不需要做任何事情来限制特定的域。

另一方面，如果你确实需要与子域共享你的 cookie，你可以这样做:

```
c := Cookie{
  // Defaults to host-only, which means exact subdomain
  // matching. Only change this to enable subdomains if you
  // need to! The below code would work on any subdomain for
  // yoursite.com
  Domain: "yoursite.com",
} 
```

Enter fullscreen mode Exit fullscreen mode

**注:** *关于如何解析域名的更多信息，参见[https://tools.ietf.org/html/rfc6265#section-5.1.3](https://tools.ietf.org/html/rfc6265#section-5.1.3)。你也可以看到源代码，它在[https://golang.org/src/net/http/cookie.go#L157](https://golang.org/src/net/http/cookie.go#L157)获得默认值。您也可以阅读[this stack overflow question](https://stackoverflow.com/questions/18492576/share-cookie-between-subdomain-and-domain)了解更多关于为什么您不需要像以前那样为子域 cookies 使用句点前缀的信息，并且链接的 Go 代码还显示，如果您提供前导句点，它将被删除。*

除了限制到特定的域，你还可以限制你的 cookies 到特定的路径。

```
c := Cookie{
  // Defaults to any path on your app, but you can use this
  // to limit to a specific subdirectory. Eg:
  Path: "/app/",
} 
```

Enter fullscreen mode Exit fullscreen mode

TL；你可以用类似于`/blah/`的东西来设置路径前缀，但是如果你想了解更多关于这个域是如何工作的，你可以查看[https://tools.ietf.org/html/rfc6265#section-5.1.4](https://tools.ietf.org/html/rfc6265#section-5.1.4)。

## 为什么不直接用 jwt？

这将不可避免地出现，所以我将简要地解决它。

尽管许多人可能会告诉你，cookies 可以像 jwt 一样安全。事实上，jwt 和 cookies 实际上并没有解决同样的问题，因为 jwt 可以存储在 cookies 中，使用起来几乎和作为标题提供时一样。

不管怎样，cookies 可以用于非认证数据，即使在这些情况下，我发现了解适当的安全措施也是有用的。

## 你喜欢这篇文章吗？加入我的邮件列表！

如果你喜欢这篇文章，请考虑将[加入我的邮件列表](https://www.calhoun.io/#subscribe)。

我大概每周会给你发一封邮件，让你知道我最近正在写的或者已经发表的新文章(比如这篇)或者视频( [ex](https://www.calhoun.io/lets-learn-algorithms-stacks-what-they-are-and-how-to-implement-them-in-go/) )。没有垃圾邮件。不要出售你的电子邮件。没什么见不得人的- *我会像对待自己的收件箱一样对待你。*

作为对您加入的特别感谢，我还将向您发送我的课程 [Web Development with Go](https://www.usegolang.com) 的截屏和电子书样本。