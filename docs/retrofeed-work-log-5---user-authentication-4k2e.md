# 反馈工作日志#5 -用户验证

> 原文：<https://dev.to/horia141/retrofeed-work-log-5---user-authentication-4k2e>

我们还没有脱离设置反馈的困境，但是我们已经到了一个非常有趣的阶段——设置用户认证。

当前的文件结构与上一篇文章中的文件结构相同。这是:

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

我们的重点是`auth.ts`和`src/services`源。

nodejs / express land 认证的*标准*选择是[护照](http://www.passportjs.org/)。这对于 NestJs 来说无疑是有效的。不过，正如我们将看到的，我们将在较低的抽象层次上操作。最重要的是，我计划使用 [auth0](https://auth0.com/) 作为*身份提供者*。

首先，有些问题。我们面对的是身份认同。auth systems 的工作是验证身份(身份验证)并在此基础上检查授权。两者都是重要的操作。但是因为我们是一个小的操作，所以我们现在只关注 auth，而把 authorization 留到以后。

在这一点上，我们将真正开始处理一些代码。喘息！不要担心，这不是太多，它的大部分仍然感觉像配置。

首先，用户的*身份的概念。这来自身份提供商，我们现在只想成为 GitHub，但它可能会成长为 Bitbucket、Google、Twitter 等。基本上任何一个更“以开发者为中心”的提供商。因此，脸书或 Instagram 可能不会入选。这个身份由 Auth0 调制和管理。鉴于这提供了如此多的数据和机器，我们还能做些什么呢？嗯，我们提供用户档案的服务特定定制，这仍然是一件需要处理的大事情。*

我做的第一件事是安装几个 GitHub 应用程序。准确地说是四个，每个环境一个。这些基本上是我们向 GitHub 注册的实体，需要他们的认证服务(以及更多)。我们将需要一个类似的设置为我们的每个供应商在未来。紧随其后的是 Auth0 配置。我再次在这里创建了四个应用程序，每个环境一个，并将它们作为“连接”与 GitHub 连接起来。我禁用了默认的用户名和密码“连接”，因为我不想在这个时代有这样的麻烦。这些配置基本上或多或少是秘密，所以它们不属于这个项目的 OSS 部分。

在我们这边，有一些编码工作要做。我们需要的两样东西是*用户服务*和 passport auth 流的实现。前者定义了作为我们应用程序的用户意味着什么，以及我们存储在 Auth0/GitHub 提供的身份之上的所有额外的东西。后者很大程度上是作为 config / magick 的*代码。但稍后会详细介绍。*

用户服务是服务目录`src`中的第一个条目。你可以在`0.0.4`版本的这里查看[。一般来说，服务是我们的代码中将要发生动作的地方，因为它们是业务逻辑的实现者。](https://github.com/horia141/retrofeed/blob/v0.0.4/src/services/user/service.ts)

> 可选:这里需要简单介绍一下架构。我希望保持这种罕见，因为我真的没有足够的带宽来提供我在这里涉及的许多主题的教程级信息。不幸的是，构建互联网应用程序需要接触很多东西，即使是像这样简单的应用程序，这也是野兽的天性。但是这个特殊的话题非常重要。因为我计划保持客户端代码简单——这是在客户端机器上执行的应用程序的一部分，这意味着复杂性将转移到服务器代码上。为了便于管理，这里的代码被分成几层。内核由“服务”代码组成。比如用户服务，订阅服务等等。它们处理业务逻辑的实现，并为应用程序的其他组件服务。它们也是未来微服务架构中实际服务的种子。因为仅仅因为我们现在正在做一个整体，并不意味着我们不应该试图预测未来的发展。尤其是当它们允许更好的整体结构时。就像独立的服务一样，我希望保持同样的约束——每个服务都有独立的存储，这是访问数据的唯一方式。事务边界也应该对应于服务边界。相反，API 控制器、网页服务器端渲染控制器、cron 任务、回调处理程序等都使用服务层。对于可能的实用程序脚本，甚至是计划的作业等，也是如此。客户端将交互的 API 也将是服务之上的一个薄层。
> 
> 关于架构选择的最后一点，Postgres 将是主要的存储后端。我们将遵循一个穷人的活动采购设计。将会有实体表(用于用户、提要等)和实体事件表，描述实体上发生的操作。从这里开始，服务的每个方法应该基本上对应一个事件类型。当然会有一些技术表，比如连接表，没有这些额外的机器。

首先要做的是定义服务将依赖的表。这些是通过`migrations`目录中的`CREATE TABLE`语句添加的。实体表看起来像:

```
CREATE TYPE auth.UserState AS ENUM ('Active', 'Removed');

CREATE TABLE auth.users (
    -- Primary key,
    id Serial,
    PRIMARY KEY (id),
    -- Core properties
    state auth.UserState NOT NULL,
    agreed_to_policy Boolean NOT NULL,
    display_name Text NOT NULL,
    nickname Text NOT NULL,
    picture_uri Text NOT NULL,
    -- External foreign key
    provider_user_id Varchar(128) NOT NULL,
    -- Denormalized data
    time_created Timestamp NOT NULL,
    time_last_updated Timestamp NOT NULL,
    time_removed Timestamp NULL
);

CREATE UNIQUE INDEX users_provider_user_id ON auth.users(provider_user_id); 
```

而它的事件表看起来像:

```
CREATE TYPE auth.UserEventType AS ENUM ('Created', 'Recreated', 'Removed', 'AgreedToPolicy');
CREATE TABLE auth.user_events (
    -- Primary key
    id Serial,
    PRIMARY KEY (id),
    -- Core properties
    type auth.UserEventType NOT NULL,
    timestamp Timestamp NOT NULL,
    data Jsonb NULL,
    -- Foreign key
    user_id Int NOT NULL REFERENCES auth.users(id)
);

CREATE INDEX user_events_user_id ON auth.user_events(user_id); 
```

如你所见，东西不多。但是随着我们的进展，我们会添加更多。考虑语言偏好、全局通知设置等。可以说，所有与用户档案相关的东西。

该服务位于 [src/services/user](https://github.com/horia141/retrofeed/tree/v0.0.4/src/services/user) 下。我希望保留三个文件作为未来服务的标准。`entities.ts`是各种类型的 dto，比如`User`、`Feed`等。`events.ts`是相关事件类型，如`UserCreated`，`server.ts`是实际代码。你可以分开看，因为它们很大。

主要的玩法是在`service.ts`中用 [`UserService::getOrCreateUser`](https://github.com/horia141/retrofeed/blob/v0.0.4/src/services/user/service.ts#L41) 和 [`UserService::getUserByProviderId`](https://github.com/horia141/retrofeed/blob/v0.0.4/src/services/user/service.ts#L142) 的方法。有几件事需要注意:

*   依赖注入规则。
*   `async/await`双重规则。
*   knexjs 在服务内部被大量使用。如果事情变得太复杂，一个单独的数据访问层可能是好的，但是现在混合数据库访问和业务逻辑是合适的。

`getOrCreateUser`是神奇的地方，它是一个由 Postgres 的`insert ... on conflict do update`功能驱动的大 upsert 语句。它在 auth 流中使用 auth0 提供的数据被调用。还要注意，该语句是“原始”的。请参见下面的可选框:

> 可选项:原始查询？2018 年？怎么回事？自从我在 StackOverflow 工作以来，我一直是这些工具和微型表单/查询构建器的爱好者。如果 knex.js 支持某些东西，我会很乐意使用它，否则我就直接调用 DB。我*正在*将代码绑定到数据库。但在 2018 年，有了 Postgres/MariaDB，你*可以*做到这一点，而不会遭受痛苦。我们说的不是某个贪婪的 90 年代 DB 供应商。它们是可靠的选择，背后有大型 OSS 社区支持，不可能很快消失。无论如何，大多数软件都需要针对一个数据库进行编写和测试。所以为什么要让你的生活变得复杂呢？

好了，服务部分到此为止。现在要做的事情有登录、注销、记住用户或从 GitHub 链接数据。这就是我们讨论的第二个主题——Passport . js。Passport 是 express 的一个 auth 库。Auth0 有它的插件和 nestjs 的住宿。所以我们应该简单地整合它。

当然，事实上这是一次非常痛苦的经历。我不知道为什么，但 auth 总是这样。代码住在 [`src/auth/auth.ts`](https://github.com/horia141/retrofeed/blob/v0.0.4/src/auth/auth.ts) 。这是一个包含控制器、中间件、dto 等的大文件。我可能会在某个时候拆散他们。在任何情况下，它都是独立的，并且从技术上来说应该是集成的起点和终点。

这里发生的事情的要点是:

*   我们定义了`/real/auth`控制器，它记录了`/login`、`/logout`和`/callback`路径。
*   登录是当用户按下 UI 中的“登录”按钮时，或者当他们访问任何需要 auth 的路由，但他们还没有登录时，用户被重定向到的路径。这只是调用 passport 逻辑，它触发一系列 oauth 流，这些流执行一系列重定向，要求用户提供凭证等，最终到达..
*   回调是授权流结束的路径。这也调用了 passport 逻辑，它将调用用户服务，并最终重定向到`/admin`。
*   注销是用户按下“注销”按钮时被重定向到的路径。这只是清除 passport 会话信息并重定向到主页。
*   注意，所有这些过程都是“标准”的 web 过程——不涉及 SPAs。只是重定向和老式的 HTTP 服务。
*   所有的例子都使用 passport with express，并且通常在一些空的路由上安装一堆中间件。这让 NestJs 很不爽。所以我必须在处理程序中显式调用中间件。非正统，但它的工作。
*   `AuthStrategy`用于配置 passport 并将其与 NestJs 集成。这一点实际上工作得很好，NestJs 意识到“认证”是值得称赞的。这里是我们实际调用`getOrCreateUser`的地方，我们使用 Raynor 来确保来自提供者的数据是好的。
*   `AuthSerializer`是另一个 NestJs 扩展点，它控制如何将用户配置文件信息映射到会话的中间件存储，并隐式地映射到 cookie 本身。默认情况下，这使用整个对象，这是愚蠢的，所以这段代码只使用用户的提供者 id。这里是我们称之为`getUserByProviderId`的地方。
*   `ViewAuthGuard`是一个特殊的 NestJs 守卫，如果请求没有通过验证，它会引发一个`ViewAuthFailedException`。这又由`ViewAuthFailedFilter`处理，它简单地重定向到`/real/auth/login`路由。过滤器是全局安装的，而防护需要在路由/控制器的基础上应用。

好了，我觉得这个*够了*。接下来将是网络整合的话题——网站地图、机器人、微数据等等。