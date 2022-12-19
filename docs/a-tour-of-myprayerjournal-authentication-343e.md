# myPrayerJournal 之旅:认证

> 原文：<https://dev.to/danieljsummers/a-tour-of-myprayerjournal-authentication-343e>

*备注:*

*   这是系列文章的第 5 篇；请参见[介绍](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-introduction-jl5)中的所有内容，以及构建该软件的要求。
*   *以文本“mpj:”开头的链接是指向 myPrayerJournal 的 1.0.0 标签(1.0 版本)的链接，除非另有说明。*

在我们旅程的这一点上，我们将转移到对应用程序和 API 认证的交叉关注。虽然身份验证和授权是不同的问题，但 myPrayerJournal 中的授权检查只是“您通过身份验证了吗？”因此，虽然我们将触及授权，并且它看起来像是身份验证的同义词，但是请记住，在更复杂的应用程序中，它们并不是这样。

## 决定授权 0

[Auth0](https://auth0.com) 提供认证服务；他们专注于一件事，并把这件事做好。它们支持简单的用户名/密码认证，以及与许多其他提供商的集成。因为“极简”是我们的目标之一，所以不必构建另一个用户系统是有吸引力的。作为一个开源项目，Auth0 免费提供这些服务。他们是 [JSON Web Token (JWT)](https://jwt.io) 标准背后的组织，该标准允许 base-64 编码的加密 JSON 作为身份证明四处传递。

这个决定被证明是一个好决定。在介绍中，我们提到了在选定之前我们使用过的所有不同的框架和服务器技术。除了其中一条“没有进一步走过的路” [<sup>1</sup>](#note-1) 之外，认证都有效。对于如何使用他们的服务，他们有几种选择；您可以引入他们的库并自己托管它，您可以编写自己的库并调用他们的端点，或者您可以使用他们托管的版本。我们选择了后者。

## 在 App 中集成 Auth0

JavaScript 似乎是 Auth0 的主要语言。他们提供了一个 [npm 包](https://www.npmjs.com/package/auth0-js)来支持使用将从他们托管的登录页面返回的响应。基本流程是:

*   用户点击一个执行 Auth0 的`authorize()`功能的链接
*   用户通过 Auth0 完成授权
*   Auth0 将结果和 JWT 返回给应用程序中预定义的端点
*   该应用程序使用 Auth0 的`parseHash()`函数从 URL 中提取 JWT(一个`GET`请求)
*   如果一切正常，建立用户会话并继续

myPrayerJournal 的实现包含在`AuthService.js` ( [mpj:AuthService.js](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/auth/AuthService.js) )中。有一个文件不是源代码库的一部分；这是包含 Auth0 实例的配置变量的文件。使用这些变量，我们从 Auth0 包中配置了`WebAuth`实例；这个实例成为我们其他身份验证调用的执行点。

## 在 App 中使用 jwt

我们从简单的开始。`login()`函数只是公开了 Auth0 的`authorize()`函数，该函数将用户定向到托管的登录页面。

逻辑序列中的下一个`handleAuthentication()`由第 16 行的`LogOn.vue` ( [mpj:LogOn.vue](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/user/LogOn.vue) )调用，在我们的商店和路由器中传递。(在我们的[上一篇文章](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-api-56l5)中，我们讨论了服务器对应用程序处理的 URL 的请求应该如何简单地返回应用程序，以便它可以处理请求；这是其中的一个案例。)`handleAuthentication()`做几件事:

*   它调用`parseHash()`从请求的查询字符串中提取 JWT。
*   如果我们获得了访问令牌和 ID 令牌:
    *   它调用`setSession()`，将这些文件保存到本地存储，并安排更新(我们稍后会详细讨论)。
    *   然后，它调用 Auth0 的`userInfo()`函数来检索我们刚刚收到的令牌的用户配置文件。
    *   当那个回来的时候，它调用商店的( [mpj:store/index.js](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/store/index.js) ) `USER_LOGGED_ON`变异，传递用户概要；这种变异将概要文件保存到存储，本地存储，并在 API 服务上设置`Bearer`标记(下面还会详细介绍)。
    *   最后，它用 URL `/journal`替换当前位置(`/user/log-on?[lots-of-base64-stuff]`)；这将用户导航到他们的日志。
*   如果事情不顺利，我们会记录到控制台并弹出警报。可能有一种更优雅的方式来处理这个问题，但是在测试中，可靠地弹出这个问题的唯一方式是在幕后搞乱事情。(而且，如果人们这样做，他们就没有资格得到好的错误消息。)

让我们更深入地了解商店的`USER_LOGGED_ON`变化；从第 68 行开始。本地存储项和状态突变非常简单，但是那个`api.setBearer()`调用呢？api 服务( [mpj:api/index.js](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/api/index.js) )通过 [Axios](https://www.npmjs.com/package/axios) 库处理所有的 API 调用。Axios 支持定义应该随每个请求发送的默认头，我们将使用 HTTP `Authorization: Bearer [base64-jwt]`头告诉 API 什么用户登录了。第 18 行设置默认的`authorization`头用于所有未来的请求。(回到商店，注意`USER_LOGGED_OFF`突变(就在这上面)做了相反的事情；它清除`authorization`标头。`AuthService.js`中的`logout()`功能清除本地存储。)

此时，一旦用户登录，每个 API 调用都会发送一个`Bearer`令牌。任何组件、商店或其动作都不需要做任何不同的事情；它只是工作。

## 维护认证

jwt 的到期时间很短，通常以小时表示。让用户的认证失效是不好的！`AuthService.js`中的`scheduleRenewal()`功能安排 JWT 的幕后更新。当更新时间到来时，`renewToken()`被调用，如果更新成功，它通过`setSession()`运行结果，正如我们上面所做的，它安排下一次更新作为它的最后一步。

为此，我们必须添加`/static/silent.html`作为 Auth0 的授权回调。这是一个位于 Vue 应用程序之外的 HTML 页面；然而，`usePostMessage: true`参数告诉更新调用，它将从`postMessage`调用接收结果。`silent.html`使用 Auth0 库解析散列并将结果发送到父窗口。[2<sup>2</sup>T7】](#note-2)

## 在 API 中使用 jwt

现在我们向 API 发送了一个`Bearer`令牌，API 可以判断用户是否登录。当我们深入研究 API 时，我们看到了一些帮助我们做到这一点的处理程序。让我们回头看看这是怎么回事。

不过，在我们看处理程序之前，我们需要看一下包含在`Program.fs` ( [mpj:Program.fs](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Program.fs) )中的配置。你可能记得长颈鹿坐在 ASP.NET 地核顶上；我们可以利用它的`JwtBearer`方法来设置一切。第 38-48 行对我们来说很有趣；我们使用`UseAuthentication`扩展方法来设置 JWT 处理，然后使用`AddJwtBearer`扩展方法来配置我们特定的 JWT 值。(与应用程序一样，这些是不在存储库中的文件的一部分。)这种配置的最终结果是，如果有一个作为有效 JWT 的`Bearer`令牌，`HttpContext`的`User`属性有一个`ClaimsPrincipal`类型的实例，并且来自 JWT 有效负载的各种属性在该用户上被注册为`Claims`。

现在我们可以把注意力转向处理程序了( [mpj:Handlers.fs](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Handlers.fs) )。第 72 行的`authorize`调用第 50-51 行定义的`user ctx`。所有这些只是寻找一个类型为`ClaimTypes.NameIdentifier`的声明。这可能是不直观的，因为其来源是来自 JWT [<sup>3</sup>](#note-3) 的`sub`属性。一个拥有`sub`声明的有效 JWT 就是我们告诉自己有一个登录用户的方式；经过身份验证的用户被视为授权用户。

您可能已经注意到，当我们描述 API 的实体时，我们没有提到`User`类型。原因很简单；它存储的唯一用户信息是`sub`。`Request`由用户 ID 分配，并且用户 ID 包含在对请求进行任何更改的每次尝试中。这消除了 URL 黑客或流氓 API 发布能够从 API 获得任何有意义的东西。

就在`user`函数下面的`userId`函数提取这个声明并返回它的值，并在`Handlers.fs`的剩余部分使用它。`add`(第 160 行)用它来设置新请求的用户 ID。`addHistory`(第 192 行)和`addNote`(第 218 行)都使用用户 ID 以及传递的请求 ID，在添加历史或注释之前尝试检索请求。`journal`(第 137 行)使用它通过用户 ID 检索日志。

我们现在有了一个完整的应用程序，同一个用户会话提供对 Vue 应用程序的访问，并将所有 API 调用绑定到该用户。我们还使用它来维护用户之间的数据安全，同时真正将所有用户数据外包给微软或谷歌(目前注册的两家外部提供商)。不过，我们的巡演还有几站。接下来是后端数据存储。

* * *

 *对不起，榆树；不是你，是我…*

 *这确实有效，但不是无限期的；如果我让前一天打开的同一个浏览器窗口保持打开状态，我仍然需要重新登录。我很可能“做错了”通过创建这个项目，这可能是我经历最多的一个领域。*

 *我不会分享我花了多长时间才算出`sub`映射到那个；让我们把它归类为“太长了。”在我的测试中，这是唯一一个没有以 JWT 的名字出现的声明。*