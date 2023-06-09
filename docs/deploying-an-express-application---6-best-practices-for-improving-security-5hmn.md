# 部署快速应用程序-提高安全性的 6 个最佳实践

> 原文：<https://dev.to/blizzerand/deploying-an-express-application---6-best-practices-for-improving-security-5hmn>

部署是软件开发生命周期的最后阶段，此时消费者和最终用户可以随时使用应用程序或 API。与应用程序处于活动开发阶段不同，一旦部署，应用程序可能会面临潜在的风险，因为端点是公开的。

开发和生产环境通常有不同的设置，因为它们有不同的需求。开发阶段所需的东西在生产时可能并不重要。举例来说，在开发阶段，帮助调试工作的详细错误日志可能具有最高优先级，然而，在生产阶段，安全问题成为优先事项。

类似地，在开发阶段，可伸缩性、性能或可靠性不是您需要解决的领域，但是，这些在生产阶段变得至关重要。

在本帖中，我们将解决其中的一些问题，并涵盖您需要了解的关于部署最佳实践的所有内容。

## 使用不推荐使用或易受攻击的 Express 版本

如果您仍在使用 Express 2.x 或 Express 3.x，您应该知道这些不再被主动维护或监控。因此，使用它们可能不是最好的主意。如果您尚未迁移到 4.0 版本，您可以在此处遵循[迁移指南。](https://expressjs.com/en/guide/migrating-4.html)

此外，您需要确保您没有使用任何已知易受攻击的 Express 版本。你可以在[安全更新页面](https://expressjs.com/en/advanced/security-updates.html)上看到这些——如果是，你需要更新到最新的稳定版本。

## 使用 TLS 进行加密

如果您正在开发的应用程序使用敏感数据，最好使用传输层安全性或 TLS。TLS 可用于保护您的连接和数据。

在数据从客户端传输到服务器之前，TLS 会对数据进行加密。这防止了最常见和最容易的黑客攻击。

鉴于 Ajax 和 POST 请求有时并不明显，可能看起来“隐藏”在浏览器级别，它们生成的网络流量容易受到数据包嗅探和中间人攻击。您可能已经熟悉 SSL 加密，但是，TLS 可以被认为是一个更高级的版本。我

如果您已经在使用 SSL，您可能需要考虑升级到 TLS。我们建议您使用 Nginx 来处理 TLS。作为参考，你可以看看——另一个获得免费 TLS 证书的有用工具是[‘让我们加密](https://letsencrypt.org/about/)’。这是一个免费的，自动化的和开源的认证机构，由 ISRG 互联网安全研究小组提供给你。

## 使用头盔防御漏洞

当涉及到保护你的应用程序免受众所周知的网络漏洞时，头盔是一个方便的工具。它的安全措施是确保 HTTP 头的最合适的设置。

归根结底，Helmet 只不过是 9 个较小的中间件函数的集合，这些函数定义了与安全相关的 HTTP 头。这些包括–

1.  CSP–设置内容安全策略标头，以帮助防止跨站点脚本攻击和各种其他站点间注入
2.  hidePoweredBy–这消除了 X-Powered By 标头
3.  hpkp——它添加了公钥固定头，以防止任何使用各种伪造证书的中间人攻击
4.  hsts–hsts 设置了严格的传输安全头，它通过 SSL/TLS 上的 HTTP 强制执行与服务器的安全连接。
5.  noCache–noCache 设置一个 Cache-Control 和 Pragma 头来禁用任何面向客户端的缓存
6.  noSniff–设置一个 X-Content-Type-Options，可以防止浏览器通过 MIME 嗅探从声明的内容类型生成的响应。
7.  frame guard–frame guard 设置 X-frame-Options 标头以允许点击劫持保护
8.  XSS filter–XSS filter 建立了一个 X-XSS 保护，在最新的 web 浏览器中支持跨站点脚本或 XSS。

要安装头盔，您可以像安装任何普通模块一样进行操作-

```
$ npm install -save helmet 
```

Enter fullscreen mode Exit fullscreen mode

之后，您现在可以通过–
在代码中使用它

```
// ...
var helmet = require( 'helmet' )
app.use(helmet())

// ... 
```

Enter fullscreen mode Exit fullscreen mode

## 饼干的安全用法

为了确保 cookie 不会使您的应用程序受到恶意攻击，建议您不要使用默认的 cookie 名称。但是，您可以选择设置预定义的 cookie 安全选项。这些在两个广泛的中间件 cookie 会话模块中可用——

1.  express.session 它取代了 Express 3.x 内置的 express.session 中间件
2.  cookie-session——它取代了 Express 3.x 内置的 express.cookieSession 中间件

这两个模块之间的区别在于它们从 cookie 会话中保存数据的方法。快速会话中间件将其会话数据存储在服务器上。这仅保存 cookie 中的会话 ID，而不保存会话数据。

除非另行编程，否则快速会话利用内存中的存储功能，并且不是为生产环境设计的最佳选择。

与快速会话中间件相比，cookie 会话中间件利用了 cookie 支持的存储。它对 cookie 中的整个会话进行消毒，而不仅仅是产生一个 cookie 密钥。建议最好在会话相对较小且易于编码时使用。

此外，另一个标志是，最好注意 cookie 数据将对客户端可用，因此，如果需要保密，这是要记住的事情。

使用默认的会话 cookie 名称可能不是一个好主意

如果您使用默认的会话 cookie 名称，您的应用程序就会受到攻击。安全标志类似于 X-Powered-By:一个潜在的攻击者，它对服务器进行指纹识别，并据此确定攻击目标。

为了避免这个问题，您可以使用通用的 cookie 名称，比如使用快速会话中间件:

```
var session = require('express-session')
app.set('trust proxy', 1) // trust first proxy
app.use(session({
  secret: 's3Cur3',
  name: 'sessionId'
})) 
```

Enter fullscreen mode Exit fullscreen mode

### 设置 cookie 安全性的选项

为了增强下列 cookie 选项的安全性，您可以-

1.  安全–确保浏览器仅通过 HTTPS 发送 cookie。
2.  HTTP only——这确保了 cookie 只通过 HTTP(S)发送，而不是客户端 JavaScript。这有助于防范跨站点脚本攻击。
3.  域–这表示 cookie 的域。域可用于与请求 URL 的主机服务器的域进行比较。
4.  path–这表示 cookie 的路径。路径可以用来与请求路径进行比较。如果发现路径和域字段匹配，就在请求中发送 cookie。
5.  expires–这用于指定重复 cookies 的到期日期。

下面是一个如何使用 cookie 会话中间件的例子:

```
var session = require('cookie-session')
var express = require('express')
var app = express()

var expiryDate = new Date(Date.now() + 60 * 60 * 1000) // 1 hour
app.use(session({
  name: 'session',
  keys: ['key1', 'key2'],
  cookie: {
    secure: true,
    httpOnly: true,
    domain: 'example.com',
    path: 'foo/bar',
    expires: expiryDate
  }
})) 
```

Enter fullscreen mode Exit fullscreen mode

## 避免已识别的漏洞和其他安全问题

如前所述，监视[节点安全项目](https://www.npmjs.com/advisories)和 Snyk 更新是一个好主意，它们可能会影响应用程序使用的 express 包和其他节点模块。有些资源非常适合与不断发展的节点安全性保持同步。以下是一些通用的安全资源，您可以使用它们来跟上应用程序安全的最新趋势:

1.  NPM 的安全博客 - NPM 的官方博客是让你了解 NPM 和 Nodejs 的重要资源。
2.  [Exabeam 的安全博客](https://www.exabeam.com/information-security-blog/) - Exabeam 的安全博客专注于事件报告和遏制漏洞表面所需的步骤。
3.  他们有一份令人印象深刻的安全清单，如果你正在使用 Nodejs 开发服务器，你不应该错过。

## 确保你所有的依赖都是安全的

使用 Npm 来确保应用程序的依赖性既健壮又方便，这是您所能期望的最好结果。然而，大多数开发人员使用的包有时可能包含严重的安全漏洞，这对您的内部应用程序有不利影响。

自从 npm@6 发布以来，npm 现在会自动检查每个请求的安装。

此外，您还可以使用“npm 审计”进行分析—

```
'$ npm audit' 
```

Enter fullscreen mode Exit fullscreen mode

如果您想确保更高的安全性，您也可以考虑使用 Snyk。

Snyk 与 Github 合作提供了一个[命令行工具](https://www.npmjs.com/package/snyk)，可以针对已知的开源数据库漏洞为你的应用提供审计服务。你可以在下面安装命令行界面:

```
$ npm install -g snyk
$ cd your-app
To check your application for vulnerabilities, you can use the following command - 
$ snyk test
You can implement the command below to open a wizard that will apply updates and patches to fix uncovered vulnerabilities as they are discovered:
$ snyk wizard 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

我们已经介绍了创建 Express 应用程序的一些最佳实践。常见的生产阶段安全最佳实践包括

1.  避免使用过时或易受攻击的 express 版本
2.  使用 TLS
3.  使用头盔包
4.  以安全的方式实现 cookies
5.  避免已知漏洞
6.  确保您的所有依赖项都是安全的和最新的

那么，你对保护你的快递申请有什么想法？请在评论中分享它们。