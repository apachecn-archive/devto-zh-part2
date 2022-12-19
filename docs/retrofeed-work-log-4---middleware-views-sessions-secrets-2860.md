# 反馈工作日志#4 -中间件、视图、会话、秘密

> 原文：<https://dev.to/horia141/retrofeed-work-log-4---middleware-views-sessions-secrets-2860>

到目前为止，我们已经为普通的 web 编程设置了一个 NPM 包和 NestJs。在这一期的[反馈](https://horia141.com/retrofeed.html)中，我们将继续使用*标准* web 应用程序设置。我们将讨论我们使用的通用中间件、会话，并以一些设计想法结束。

当前文件结构或多或少:

```
.
├── Dockerfile
├── config
│ ├── env.local
│ └── env.test
├── knexfile.js
├── migrations #hidden
├── package.json
├── scripts # hidden
├── secrets.tar.enc
├── src
│ ├── auth
│ │ └── auth.ts
│ ├── controllers
│ │ ├── app
│ │ │ ├── admin.hbs
│ │ │ ├── app.ts
│ │ │ ├── home.hbs
│ │ │ └── layout.hbs
│ │ └── tech
│ │ └── status.ts
│ ├── index.ts
│ ├── infra
│ │ ├── common.ts
│ │ ├── config.ts
│ │ ├── db-conn.ts
│ │ ├── request-id-middleware.ts
│ │ ├── request-time-middleware.ts
│ │ ├── request-version-middleware.ts
│ │ └── session-middleware.ts
│ └── services
│ └── user
│ ├── entities.ts
│ ├── events.ts
│ └── service.ts
├── test # hidden
└── test-e2e #hidden 
```

研究的版本是 [v0.0.4](https://github.com/horia141/retrofeed/tree/v0.0.4) 。它还有一整套与身份验证相关的更改。但是我会在下一篇文章中介绍这些内容。关于上面的树，我隐藏了一堆与讨论无关的文件和目录。无论如何，你可以看到`src`树变得更强壮了。

Express 和 NestJs 预装了许多好吃的东西。然而，因为你可以用它们来构建微服务和常规的 web 应用程序，所以很多事情都是可选的。因此，我们必须做的第一件事是，在上一篇文章的基础上，为常规的 web 应用程序开发设置一些*必备的*。幸运的是，它们将采用的常见形式是应用于所有路由或几乎所有*路由的中间件，或者某个全局交换机告诉 NestJs 做些什么。*

在`src/index.ts`中，`boostrap`函数中有一堆额外的配置。

```
async function bootstrap() {
    const app = await NestFactory.create(MainModule);
    const config = app.get(Config);
    app.setBaseViewsDir(join(__dirname, "controllers/app"));
    app.setViewEngine("hbs");
    app.use(helmet());
    app.use(compression());
    app.useGlobalFilters(new ViewAuthFailedFilter());
    await app.listen(config.port);
} 
```

首先设置的是 [*视图引擎*](https://expressjs.com/en/guide/using-template-engines.html) 。Express 的都可以用，我选了[车把](https://handlebarsjs.com/)。没有什么特别强烈的理由，事实上几乎任何人都可以这样做。这个项目在这里不需要巨大的复杂性。我将在“渲染哲学”中涉及更多。

另外两行是用默认设置设置[头盔](https://helmetjs.github.io/)和使能[压缩](https://github.com/expressjs/compression)。第一个特别重要，因为它提供了面向浏览器的安全措施的基线——DNS 预取控制、[帧保护](https://github.com/helmetjs/frameguard)、 [HSTS](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security) 等。随着项目的进展，我还会启用更多的功能，尽管对于像这样的小项目，成本效益分析看起来不太好(例如公钥锁定)。第二个中间件是不言自明的——它支持响应压缩。

另一个简单且不错的中间件是*请求 id* 中间件。这将在请求-响应对中附加一个全局唯一标识符字符串。id 作为`res.requestId`可用于应用程序。它还作为`X-RetroFeed-RequestId`头附加到响应中。这对日志记录和以后的分析非常有用，因为它可以用作日志条目的*主键*。应用程序级日志、外部调用等。也可以使用同一个 id，所以应用程序执行的所有*动作*可以在以后组合在一起。没有好的打字稿可以用，所以我就在 [`src/infra/request-id-middleware.ts`](https://github.com/horia141/retrofeed/blob/v0.0.4/src/infra/request-id-middleware.ts) 里加了一个小的。

同样的还有 [`src/infra/request-time-middleware.ts`](https://github.com/horia141/retrofeed/blob/v0.0.4/src/infra/request-time-middleware.ts) 的请求时间中间件。这仅仅为应用程序暴露了一个`req.requestTime`字段，因为`Date`头已经为外部使用提供了这个目的。我计划做一些类似于[事件源](https://martinfowler.com/eaaDev/EventSourcing.html)的事情，让一个请求产生的所有突变有一个共同的时间，这将有助于实现。

最后一个*小*中间件是 [`src/infra/request-version-middleware.ts`](https://github.com/horia141/retrofeed/blob/v0.0.4/src/infra/request-version-middleware.ts) 的应用版本一。这只是将`X-RetroFeed-Version`头附加到响应上。对于 [`LOCAL`和`TEST`](https://horia-141.com/retrofeed-3.html) 环境，这被简单地设置为`latest`，但是`staging`和`live`环境将接收类似于`v0.1.4`或任何实际版本的应用程序。该值来自配置，构建&部署流程将负责在生产中正确设置它。

当设置*会话*时，事情开始变得复杂。同样，本着尽可能从生态系统中使用的精神，我使用了 [`express-session`](https://github.com/expressjs/session#readme) 中间件和 [`connect-session-knex`](https://github.com/llambda/connect-session-knex) 存储。如前所述，我们将使用 [`knex`](https://knexjs.org/) 作为一个微 ORM，并且很好地为会话搭载它的所有基础设施。*比较大的*配置在 [`src/infra/session-middleware.ts`](https://github.com/horia141/retrofeed/blob/v0.0.4/src/infra/session-middleware.ts) 里。`express-session`按照你想象的方式工作——它在回应上设置了一个 cookie，然后通过通常的机制传播。这个 cookie 由我们需要提供的一个秘密*签名*(参见下面的秘密管理)，它包含一个标识符`sid`，用于从存储中查找“会话级”信息。我们将在下一篇文章中讨论的认证系统将会很好地使用这种机制。出于我们的目的，会话*永远不会过期* [1】，并且在创建后或多或少是不可变的。cookie 也是 [http only](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#Secure_and_HttpOnly_cookies) ，在非本地环境中是安全的，并且有一个 [`SameSite=Lax`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#SameSite_cookies) 设置。

在 [`migrations/0002.create-sessions-table.js`](https://github.com/horia141/retrofeed/blob/v0.0.4/migrations/0002.create-sessions-table.js) 迁移中，我们设置了下面的`auth.sessions`表，供 knex 商店使用。它的结构是:

```
CREATE TABLE auth.sessions (
    sid VARCHAR(255) PRIMARY KEY,
    sess JSON NOT NULL,
    expired TIMESTAMP WITH TIME ZONE NOT NULL
);
CREATE UNIQUE INDEX sessions_expired ON auth.sessions(expired); 
```

这是一张*技术表*，而不是一张*实体表*之一。因此，我们在它上面没有一个`session_events`或者任何额外的东西。[2]

`helmet`、`compression`和我们的小型定制中间件适用于全球所有路线。与会话和授权相关的并不是全局需要的，或者以不同的方式需要。例如，我们不关心`/status`呼叫的会话。或者对于 API 调用，我们不想创建会话，而是假设它存在。为此，我们有四种类型的*路线*:

*   应用程序路由——它们以某种方式生成应用程序的 UI。我们还将在这里输入各种提要。我们希望所有的中间件都在这里，并且可以创建会话。
*   API 路由——这些是供浏览器端 SPA 代码与后端交互的。我们希望所有的中间件都在这里，我们假设存在一个会话，并且永远不会手工创建一个。假设人类用户必须首先访问一个页面(并获得一个会话)，然后进行 API 调用。
*   集成路径——这些是像`/robots.txt`处理程序、各种域检查、`browser-config.xml`、webhooks、auth 处理程序等等。我们希望所有的中间件都在这里，但会话除外。
*   技术路线-状态检查，开发者管理面板，调试页面，API 交互页面等。同样，我们希望所有的中间件都在这里，但会话除外。这些应该限制外部世界的访问。

为了比较效果，一个`GET /`用来返回一些基本的东西，比如:

```
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 19
Content-Type: text/html; charset=utf-8
Date: Sat, 11 Aug 2018 12:29:10 GMT
ETag: W/"13-yJNY7FvAzljr3cH9QOT6Sna+sPs"
X-Powered-By: Express

Hello World - LOCAL 
```

现在它产生了:

```
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 232
Content-Type: text/html; charset=utf-8
Date: Sat, 11 Aug 2018 12:30:02 GMT
ETag: W/"e8-+wYItcFKggKlbJpYhiJU65HnWwk"
Strict-Transport-Security: max-age=15552000; includeSubDomains
Vary: Accept-Encoding
X-Content-Type-Options: nosniff
X-DNS-Prefetch-Control: off
X-Download-Options: noopen
X-Frame-Options: SAMEORIGIN
X-RetroFeed-RequestId: y1orkfd2JbhCKZnWr4xfP0dB6tIQVvHt
X-RetroFeed-Version: latest
X-XSS-Protection: 1; mode=block
set-cookie: retrofeed.session=s%3A8ra0QxZqCmJJV2PuiPzHJlZ78profFCu.A2uIakPpIRqU9aLfur41V%2FHqN5Ec57w3FXI38pzvuWM; Domain=localhost; Path=/; Expires=Tue, 10 Aug 2060 12:30:02 GMT; HttpOnly; SameSite=Lax

<!doctype html>
...rest of content 
```

在设置中间件时，我使用了[快速中间件列表](http://expressjs.com/en/resources/middleware.html)来感受一下*常见的*中间件，我不应该错过它们。对于整个项目，我想尽可能避免重新发明轮子，拥有这些中间件/最佳实践/通用方法的列表真的很有帮助。

在这篇文章的结尾，我将讨论三个杂项话题。但这些有趣的事情宜早不宜迟。

首先- *环境*。大多数代码将在*多个地点*运行。至少是开发人员的机器，以及它最终应该工作的地方(工厂、汽车、web 服务器等)。我选择定义四种环境，它们是:

*   开发者自己的机器，而他们正在本地构建东西。
*   `TEST` -运行测试的 Travis 机器/作业&进行部署。
*   `STAGING`-GCP 上的一台或多台机器，不对公众开放，用于大规模测试、捣乱等。`LIVE`环境的小规模克隆。
*   `LIVE`-GCP 上的一台或多台机器，对公众开放，存储*真实用户数据*。这就是奇迹发生的地方。

我把后两种环境称为*生产*，把前两种称为*开发*。 [`src/infra/common.ts`](https://github.com/horia141/retrofeed/blob/v0.0.4/src/infra/common.ts#L13) 包含应用程序中处理这种情况的代码。环境变量`$ENV`定义当前配置，这是在外部设置的*。除非另有说明，否则假定为`LOCAL`，或者在 Travis 的配置中设置，或者由每台机器上的部署过程设置。它还出现在配置文件中，因此应用程序可以通过`src/infra/config.ts`访问它。*

说到配置，它们存在于`config/env.$ENV`文件中，正如前面提到的 [`scripts/setup-env.sh`](https://dev.to/horia141/retrofeed-work-log-3---web-serving--more-setup-244o-temp-slug-9110917) 负责选择正确的配置。在这个版本中还有`config.$ENV.secrets`文件，其中保存了那些不应该*公开*的设置——API 密钥、秘密令牌、密码等。因为存储库是公共的，所以这些文件不会被提交。取而代之的是提交一个包含它们的`secrets.tar.enc`文件。顾名思义，这是一个加密的档案。为此，我使用了 Travis 的[文件加密](https://docs.travis-ci.com/user/encrypting-files/)功能。Travis 持有密钥，构建脚本将在构建时使用它来解密归档文件，并使用它的内容来运行测试并在正确的环境中进行适当的部署。目前我持有一份未加密文件的副本，任何其他提交者也必须得到一份。如果对机密文件进行了更改，应该运行`yarn prepare:config`命令，这样非本地环境也可以获得更改。

最后说一下*古典渲染*。现在单页应用程序风靡一时，你可能会认为在某个地方推一个脚手架`index.html`页面和一堆`.js` / `.css`文件就足够了，然后你就可以收工了。可悲的是，以我的经验来看，这是很多悲伤的原因。首先，有很多事情是水疗所不能覆盖的。一切可以被称为“网络整合”的东西都符合这个要求(`robots.txt`、`humans.txt`、`sitemap.xml`、RSS 提要等等)。第二，授权流*几乎总是*需要大量重定向，webhooks 可能需要非 JS 内容等等。第三，您可能需要让服务器在将捆绑的 JS 文件发送给客户机之前对其进行处理。例如，插入 API 键、添加会话信息、进行服务器端渲染等。所以有一个好的*经典的*服务器端渲染系统设置是值得的，这几乎总是意味着一个 [*视图引擎*](https://stackoverflow.com/questions/8308485/what-is-view-engine-what-does-it-actually-do)【4】。我并不提倡我们去用它们做大部分 UI 工作，因为 React/Vue/Angular 有它们的优点，它们有助于构建更好的应用程序。然而，我有一种感觉，这个应用程序不需要 React 的全部功能-只需要 React 和一个路由器，但没有 Redux 的东西。所以在我看来，React 中服务器端渲染和中等大小的 UI 之间的折中是最好的。在重大转变时重新加载页面是值得的。

总之，这个帖子到此为止。它*又一次*变得比预期的更大，但现在我们肯定已经走出了*刚刚建立*的阶段，更多地走在实实在在的道路上。

点击查看系列[中的所有文章。](//https//horia141.com/retrofeed.html)

* * *

[1]严格来说，它们将在 2060 年或更远的某一年到期。这里使用的会话的目的是对特定的*用户*在所有时间的交互进行分组。如果需要，稍后将添加额外的处理，以将其拆分为实际的 *UX 交互会话*。如果我们假设会话，那么关于认证的事情也很简单，因此认证状态*一旦设置就不会自动过期*。否则，我们(1)需要添加各种错误处理代码来处理连续操作之间的会话过期，或者(2)接受用户有时的糟糕体验，因为操作会因为会话过期而失败。

[2]这是我虚构的分离，尽管它*可能*有一个名字。像 *users* 、 *feeds* 、 *cars* 、 *books* 这样的表代表实体，最好有一个 *$entity_events* 表，其中记录了该实体的所有变更。这是一种轻量级的事件源形式。其他表的存在只是为了记账，或者分析，或者简单的非实体工作(连接表？).我把前一个*实体表*，后一个*技术表*。

[3]除非你真的很喜欢因为一个小小的表单动作而重新加载页面。

[4]当然，服务器端的渲染并不局限于此。你基本上是在一个套接字上发送一个字节流。你可以使用字符串连接，各种*页面*解决方案(PHP，JSP，ASP 等)。但是我认为模板引擎解决方案，比如把手，Django 的视图等等，确实没有争议。更加可口。