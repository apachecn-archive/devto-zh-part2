# 揭秘 SOP 和 CSP，CORS 特色

> 原文：<https://dev.to/artis3n/demystifying-sop-and-csp-featuring-cors-16o3>

注意:这篇文章是通过 RSS 从我的博客同步的。在这个过程中，一些图像格式看起来已经被弄乱了。如果你在阅读这篇文章时有任何问题，请在我的博客上查看，我会解决图像格式的问题。

根据我的经验，许多开发人员不知道同源策略，也不知道内容安全策略，或者至少不知道 CSP 支持一两个以上的指令。让我们来看看这些术语是什么，以及它们如何显著提高网络的安全性。

Mozilla 的 MDN 文档描述如下:

[同源政策](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy):

> 限制从一个来源加载的文档或脚本如何与另一个来源的资源进行交互。它有助于隔离潜在的恶意文档，减少可能的攻击媒介。

[内容安全策略](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP):

> 一个附加的安全层，有助于检测和缓解某些类型的攻击，包括跨站点脚本(XSS)和数据注入攻击。这些攻击被用于从数据窃取到网站毁损到恶意软件传播的各种事情。

太好了！谜团解开了，我们可以回家了。你现在开悟了。

没有吗？好吧，让我们把这些术语分解一下。

# 同源策略

同源策略(SOP)实际上是 web 安全模型的基础。在这种模式下，每个网站都有自己的来源，与互联网的其他部分隔离开来。这些孤立的源不能从另一个源读取或修改数据。这意味着一个源(如 reddit.com)上的资源不能修改或读取另一个源(account.yourbank.com)上的数据。SOP 由浏览器强制执行。它的限制可以通过一个服务器响应头来放松，我们稍后将讨论这个问题。

如果资源具有相同的三个属性，SOP 会将它们分组到相同的源下:

1)主机
2)协议
3)端口

SOP 阻止了以下相互作用:

[![Different host SOP violation](img/6079e974bb51fc898d86ec67cce7b084.png)T2】](///assets/sop_csp/sop_host_malicious-8fe28dbbd55b526a7440ab6bc9793aabdacaf908f26fc69d1ed248a53e83c487.svg)

由于 mysite.com 和 malicious.com 显然是不同的主机，SOP 阻止了这些不同来源之间的数据共享。

[![Different protocol SOP violation](img/be3205395efd37f01e19fc32ce03b2c8.png)T2】](///assets/sop_csp/sop_protocol-22271c4ec18686d544c9eac0b72ab7e03faa444214bd6fbd57e6738765544b64.svg)

即使我们现在在同一个主机下，但是 HTTPS 和 HTTP 是不同的协议，所以 SOP 阻止了数据交互，把这两个原点分开了。

[![Different port SOP violation](img/01c44f6cf8195143c914c4b50c8c4008.png)T2】](///assets/sop_csp/sop_port-dad1f75508d508aba662e55a343f3d489fac4bbbbe073b7431651ca0cbb0948b.svg)

由于 443 和 4443 是不同的端口，SOP 阻断了数据交互，将这两个原点分开。

[![Different port SOP violation](img/dea0f29507754956f9d34234532ade1e.png)T2】](///assets/sop_csp/sop_host_mysite-15d7cd99be2c26146fee697651737113509150b0006b986e0f6a9d1422cdb0ae.svg)

与第一个例子类似，子域被认为是独立的主机。SOP 将阻止两个子域之间的数据交互。

## 了解同源政策

当然，这可能会引出这样一个问题，“但是我需要从另一个来源访问文件！“我有一个下载 jQuery 的 CDN，我需要从谷歌获得分析，NewRelic 正在监控我的网站，我需要修改一个子域，”

嗯，这些都可以。SOP 根据资源执行的位置阻止来自不同来源**的数据共享。从另一个来源获取并在您的站点上执行的 JavaScript 被归入您站点的来源。例外是你网站上的另一个子域，这是 SOP 不允许的。**

## 通过 SOP 启用子域

为了让同一个超级域的两个子域共享数据，[你必须为这些子域重新定义**根域**](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy#Changing_origin) 。例如，如果 a.mysite.com 的**和 b.mysite.com 的**想要读取或修改对方的数据，SOP 会阻止这种行为。为了改变这一点，**a.mysite.com**和**b.mysite.com**必须都运行一个脚本来设置:**** 

```
document.domain = "mysite.com" 
```

Enter fullscreen mode Exit fullscreen mode

由于**mysite.com**是**a.mysite.com**的超级域，所以允许这个设置(同样适用于**b.mysite.com**)。禁止您将文档的域更改为您无法控制的域。在站点**上运行`document.domain = "google.com"`a.mysite.com**将会失败。

[![SOP superdomain violation prevented](img/6211adceeb1d72d643459407a93b290e.png)T2】](///assets/sop_csp/sop_domain_violation-7cc8313a271f3758b31964d4c299d4b2b494551672574722d1ad340cfe58d687.svg)

如果**a.mysite.com**和**b.mysite.com**都将其文档的域更改为同一个超级域，突然两者共享相同的主机、端口和协议，SOP 将允许数据共享。

[![SOP superdomain escalation successful](img/c0ada4c68f40d9abc354ed3ea4897af7.png)T2】](///assets/sop_csp/sop_domain_changed-100e07566b6ad547819e9bdd235f6e932c60aef5f8803a4a57740536462c73e3.svg)

请注意，该站点的端口由浏览器单独持有。每当调用来设置`document.domain`的值时，SOP 的域端口值被设置为`null`。这样做是为了防止**a.mysite.com**想要修改**mysite.com**上的数据，因此**a.mysite.com**将其`document.domain`更改为`mysite.com`。主机值现在匹配了，但是**mysite.com**可能不想给子域权限来读取或修改它的内容。由于在**a.mysite.com**上进行了设置`document.domain`的调用，该子域的端口值被设置为`null`。如果**mysite.com**没有类似地调用 set `document.domain = "mysite.com"`(是的，尽管已经是`mysite.com`)，那么【a.mysite.com】T21(`null`)和**mysite.com**(大概是 80 或 443)的端口值将不匹配，确保 SOP 继续阻止数据共享。

[![SOP superdomain escalation failed](img/838b5be1b138f502fb7e70aed6f07264.png)T2】](///assets/sop_csp/sop_domain_failed-85e47dcceeed5dd584742bcfcc9c25281872e452a22cb8f4076c505ee4ceb5b8.svg)

## 那么 SOP 允许什么呢？

SOP 允许在你的站点上执行的几乎所有资源**运行跨源写入。这意味着:**

*   `script src="..."></script>`
*   `<link rel="stylesheet" href="...">`
*   `<img>`、`<video>`、`<audio>`、`<object>`、`<applet>`
*   任何`<iframe>`或`<frame>`

可以将数据写入您的页面。由于 SOP，这些资源通常不能从您的页面读取数据，只能写入数据。内容安全策略将保护您的站点免受这些资源的攻击，我们稍后将对此进行讨论。

### `about:blank`和`javascript:`

注意，从带有`about:blank`或`javascript:` URL 的页面执行的脚本继承了打开该 URL 的文档的来源。这意味着[跨站点脚本](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS)) (XSS)将会在它运行的页面的继承源上执行。此外，打开新选项卡/窗口的代码可以将内容写入其中，因为新选项卡/窗口继承了创建它的页面的来源。前者不好。后者可以“照常营业”，但经常被恶意广告和广告软件滥用(注意！你的计算机有 37 种病毒，点击这里免费扫描！).

> 这里先说清楚。CORS 是一个反安全机制。

# 跨产地资源共享

为了允许其他来源从您的站点读取数据，您必须放宽您的 SOP。您可以通过定义一个[跨源资源共享](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) (CORS)策略来做到这一点。CORS 通过 HTTP 头应用，并允许访问白名单域中的资源。在 CORS 标头中，您定义了允许从您的站点读取数据的源集合。当发生 CORS 违规时，违规的 JavaScript 不会收到任何关于其请求失败原因的信息，除此之外它确实失败了。

**这里先说清楚。CORS 是一个反安全的 T2 机制。**安全控制是同源策略，默认情况下由您的浏览器实施。通过设置 CORS 标题，你就*禁用了网站上的*安全控制来提供某些内容。这并不一定是一件坏事，为了你的网站正常运行，放松 SOP 是有正当理由的，但是环境很重要。我经常看到开发人员询问如何设置“CORS 安全控制”你不知道。您只能逐步禁用它。

在`Access-Control-Allow-Origin`标题上设置了 CORS 策略。在这个标题中，您列出了一系列允许绕过 SOP 限制的空格分隔的 URL(记住，这是为了那些白名单中的站点读取或修改您站点上的数据，而不是相反)。我经常看到不安全的`Access-Control-Allow-Origin: *`设置为头的值。我们对这个 CORS 政策怎么看？我们说*互联网上的任何*网站都有权限阅读或修改我们网站上的数据。这当然比定义和维护需要访问的网站的白名单更容易，但是请不要这样做了，好吗？过度宽松的 CORS 政策，比如`*`，导致了[大量有效的攻击](https://portswigger.net/blog/exploiting-cors-misconfigurations-for-bitcoins-and-bounties)。文章提到浏览器中的`Access-Control-Allow-Origin`支持很少。我想指出的是，文章写于 2016 年。到目前为止，每个浏览器都支持这个标题[。](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#Browser_compatibility)

## 飞行前请求

一些请求方法要求在发出跨原点请求之前发送额外的飞行前请求。[预检请求](https://developer.mozilla.org/en-US/docs/Glossary/Preflight_request)是由浏览器自动发出的选项请求。通常，这是在使用 GET 或 POST 之外的请求方法进行跨来源请求时，或者在使用某些非白名单标头时。细节在这里[列出](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#Simple_requests)，但是，同样的，你的浏览器会根据需要自动发布。

## 跟进 CORS

要了解更多关于 CORS 的信息，我强烈推荐来自 Derbycon 2019 的视频:“致 CORS，你的水疗问题的原因和解决方案。”

[https://www.youtube.com/embed/tH-HG4b4GYQ](https://www.youtube.com/embed/tH-HG4b4GYQ)

主持人用我见过的最易懂的格式解释了 CORS，并展示了几乎每种语言的 CORS 图书馆都设置了不安全的 CORS 默认设置。

# 内容安全策略

好了，现在我们对 SOP 以及 CORS 如何被用来放松 SOP 限制有了很好的理解。让我们来讨论一下内容安全策略(CSP)的适用范围。

CSP 是在`Content-Security-Policy` HTTP 报头上定义的策略。报头的传统版本是`X-Content-Security-Policy`。使用当前版本。CSP 的主要目的是防止跨站点脚本(XSS)攻击。XSS 的工作原理是欺骗浏览器在你的网站下运行脚本，让恶意代码读取或修改网站内容。我们不应该相信我们无法证实的东西！<sup id="f1">[1](#sri)</sup>CSP 允许我们定义可信内容来源的白名单。浏览器不会执行或呈现该列表之外的任何资源。如果攻击者能够在您的站点上插入脚本，该脚本将不会运行，因为它与白名单不匹配。CSP 报头可以由[多个不同的指令](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy#Directives)组成。

CSP 的一个例子可能是这样的:

```
Content-Security-Policy: script-src 'self' www.google-analytics.com 
```

Enter fullscreen mode Exit fullscreen mode

`script-src`是一个 CSP 指令。它允许您定义网页上 JavaScript 可接受来源的白名单。在这个例子中，我们允许来自当前页面来源`self`的 JavaScript，以及来自`www.google-analytics.com`的任何资源请求。任何试图在网页上执行的不是来自这两个来源的脚本都将被浏览器阻止。

该错误将如下所示:

铬合金:

> 拒绝加载脚本“script-uri”，因为它违反了以下内容安全策略指令:“您的 CSP 指令”。

火狐浏览器:

> 内容安全策略:发生了只报告 CSP 策略的违规(“执行内联脚本的尝试已被阻止”)。该行为被允许，CSP 报告被发送。

## CSP 处于上报模式

正如 Firefox 错误提示的那样，CSP 可以设置为“阻塞”模式或“报告”模式。在报告下，CSP 不会阻止任何内容，只是将警报回显到浏览器控制台上。您必须将报告模式下的`report-uri`指令设置为收集 CSP 错误消息的 web 端点。在 CSP 违规时，用户的浏览器会将 JSON 中的违规错误发布到已配置的端点。一个这样的服务来监控你的网站的违规行为是[报告 URI](https://report-uri.com/) 。报告模式的目的是确保您在完全启用 CSP 并可能破坏您的站点之前，了解您的站点上的所有资源来自哪里。您应该在您的“阻止”策略上保留一个`report-uri`指令，以继续对针对您的站点的攻击类型保持警惕，并警告任何潜在的 CSP 错误配置。

使用`Content-Security-Policy-Report-Only`作为表头，在报告模式下设置 CSP。当您准备好让 CSP 开始阻止内容时，请使用`Content-Security-Policy`作为标题。

## 内联 JavaScript

默认情况下，包含 CSP 会阻止任何内联 JavaScript 和动态代码评估，因此注入的 JavaScript 无法假定您的站点的来源。这确实意味着网页的可信 JavaScript 必须来自 JavaScript 文件，并且不能以内联方式编写。这是一个糟糕的设计模式，因此您现在可以在提高安全性的基础上证明重构的合理性。如果您确实希望在 CSP 下启用这些特性中的任何一个，您可以这样设置您的 CSP:

```
Content-Security-Policy: script-src 'unsafe-inline' 'unsafe-eval' 
```

Enter fullscreen mode Exit fullscreen mode

该策略分别允许内联 JavaScript 和动态代码评估。但是不要那么做。允许内联脚本使 XSS 重新回到桌面上，否则它将被阻止。

注意`unsafe-inline`包括`style`标签和`javascript:`URL。所有样式必须同样出现在一个单独的文件中，而不是内联的。但是值得一提的是，防止内联脚本是 CSP 最大的好处。不要启用此策略！

## CSP 指令

还有许多其他 CSP 指令，我强烈建议您通过链接页面通读它们。一些值得注意的指令:

`default-src`:对所有资源应用默认的 CSP，由特定的 CSP 指令覆盖，例如`script-src`。

`form-action`:将`form`提交的有效端点列入白名单。

`frame-ancestors`:定义允许嵌入你的网页的源，比如在他们网站的`iframe`里面渲染。启用该指令会阻止[点击劫持](https://www.owasp.org/index.php/Clickjacking)，所以启用它！

`sandbox`:为所请求的内容启用类似 iframe 的沙箱，并对其应用 CSP。`sandbox`指令的空值将所有限制应用于内容，可以通过`allow-forms`等值有选择地启用这些限制。这个指令有一点不同，因为它阻止页面可以采取的动作，而不是页面可以加载什么资源。如果指定，页面将被视为通过 iframe 加载，从而产生多种效果。关于沙盒效果的更多细节可以在这里找到[。这个特性为安全地锁定你的站点提供了很多可能性。](https://html.spec.whatwg.org/dev/origin.html#sandboxing)

`require-sri-for`:要求页面上的脚本和样式使用[子资源完整性](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity)。该指令的值选项是`script`和`style`。

`upgrade-insecure-requests`:指示浏览器将网页上的任何 HTTP 链接升级到 HTTPS。这是一个简单的方法来升级一个有很多 HTTP 链接到 HTTPS 的遗留页面。

您应该注意到，包含一个没有定义白名单的指令默认为`*`，这意味着允许一切。指定一个`default-src`指令自然会覆盖这种行为。

## 精心制定 CSP 政策

谷歌的 CSP 页面有关于从头开始制作 CSP 的极好的指导和例子。目标是确定您的站点实际上加载了哪些资源，并基于这些信息设置策略。

# 这就结束了

我希望这篇文章对于理解 SOP、CORS 和 CSP 是一个有用的资源，如果您还没有实施的话，我希望这篇文章能够帮助您在您的站点上正确地启用 CORS 和 CSP。

 <small>:对静态资源的请求，例如你知道不会改变的出售脚本，应该使用[子资源完整性](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity)作为那些资源的验证机制。[↩](#f1 "return")T7】</small>