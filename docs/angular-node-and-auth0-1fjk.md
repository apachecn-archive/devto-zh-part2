# 角度、节点和验证 0

> 原文：<https://dev.to/prestonjlamb/angular-node-and-auth0-1fjk>

在最近的一个项目中，我需要对我的 Angular 应用程序(它有一个 Node.js 后端)进行身份验证，所以我开始寻找选项，并决定使用 Auth0。有很多选择，包括使用 passport-local 和自己进行身份验证。但是为了避免独自管理用户和安全，我决定使用 [Auth0](https://auth0.com/) 的服务。

首先，我使用 Auth0 博客上的这篇文章在我的应用程序中实现认证。我不会把那篇文章中的所有内容都放到这篇博文中，但基本思想如下:

1.  在您的应用程序中实现一个`AuthService`来处理登录状态和发起身份验证操作。
2.  同一个 auth 服务在回调路由中接收从 Auth0 返回的令牌，并将其保存在`localStorage`中，以便在调用您的服务器时使用。
3.  在对服务器的每次调用中，该令牌都被放在报头中，以便在服务器上进行验证。
4.  在服务器上，验证令牌。如果有效，继续请求。否则，返回一个错误。
5.  在 Angular 应用程序中实现一个`AuthGuard`，以防止访问应用程序中需要登录的部分。

当您完成了本文的第 1 部分(我甚至没有完成第 2 部分，因为它涵盖了我不需要的东西)时，您应该确保您的应用程序只对那些经过身份验证的人可用。这一部分并没有花费太长时间，可能只需要几个小时就可以实现。真的很好，也很快。

将应用程序与 Auth0 真正集成的下一步是为用户提供更新信息的能力。他们真的没有办法直接通过 Auth0 非常容易地(或者根本不可能)做到这一点。所以我希望在这里讨论的是如何让你的用户通过你的应用程序更新他们的账户信息。

所有这些中最简单的部分是决定您希望您的用户能够更改哪些字段。对我来说，一开始就很简单:名、姓和电子邮件地址。不过，你真的可以提供你想要的任何东西。在你的 Angular 应用程序中构建一个你喜欢的表单，获取信息，然后发布到服务器。这是真正的工作开始的地方。

默认情况下，即使您在 Auth0 中设置了客户端，您也不能自动更新用户，甚至不能获取他们的个人资料信息。为此，你必须使用他们的[管理 API](https://auth0.com/docs/api/management/v2/tokens) 。要使用它，您需要在调用管理 API 之前获得一个令牌。这需要制作第二个客户端。所以我在 Auth0 的 dashboard 里做了一个非交互客户端，从客户端的设置页面里获取了`client_id`、`client_secret`和`audience`。受众是一个 URL，像`https://username.auth0.com/api/v2`。我使用了一个 Sails.js 服务器，但它只是一个节点服务器，所以以下面的方式发出请求对于所有节点服务器都是一样的。我用的是`request-promise`包:

```
getApiToken: async function() {
    const token = await request({
        uri: sails.config.auth.managementApi.tokenEndpoint,
        body: {
            client_id: sails.config.auth.managementApi.clientId,
            client_secret: sails.config.auth.managementApi.clientSecret,
            audience: sails.config.auth.managementApi.audience,
            grant_type: 'client_credentials',
        },
        method: 'POST',
        json: true,
    });

    return token;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数非常简单:它向 Auth0 发出一个`POST`请求，返回值是访问 API 所需的令牌。获得令牌后，您可以获得用户的配置文件，更新用户的配置文件，或者管理 API 中可用的任何其他功能。下面是一个获取用户资料的例子:

```
getUserProfile: async function(userId, token) {
    const userProfile = await request({
        uri: `https://username.auth0.com/api/v2/users/${userId}`,
        method: 'GET',
        headers: { authorization: `Bearer ${token.access_token}`, 'content-type': 'application/json' },
        json: true,
    });

    return userProfile;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是另一个非常简单的函数:用一个用户 id(在验证前端传递的令牌之后)和上一步获得的令牌向管理 API 发出一个`GET`请求。

更新也是同样的思路；向在`getUserProfile`函数中列出的相同 URL 发出一个`PATCH`请求，然后让管理 API 完成它的工作。API 上有许多可用的函数；这里列出的用户功能是。

需要记住的是，您可能想要更新的许多字段需要保存在`user_metadata`字段中。例如，在我的应用程序中，名和姓字段保存在`user_metadata`属性中。如果这个字段没有列在 JSON 对象的第一级[这里的](https://auth0.com/docs/api/management/v2#!/Users/get_users_by_id)或者[这里的](https://auth0.com/docs/api/management/v2#!/Users/patch_users_by_id)，那么将这些字段放在`user_metadata`字段中。同样，当更新`user_metadata`时，确保子属性*总是*拼写相同。每次更新时,`user_metadata`属性都被合并在一起，所以如果有一次使用了`firstName`,另一次使用了`firstNAme`,那么`user_metadata`对象将同时拥有这两个属性。

我确定要做的另一件事是检查用户帐户是用什么源创建的；比如社交媒体登录或用户名密码组合。用户名-密码组合意味着用户在 Auth0 的站点上创建了一个帐户来登录您的应用程序。我只允许他们改变他们的电子邮件地址，如果他们用这种方法注册申请的话。如果他们注册了，比如说，谷歌，可能有办法改变电子邮件，但是我不想通过这些步骤来验证他们更新的东西是正确的。此外，我的应用程序不会有社交登录功能。

希望这些都有助于您在应用程序中更全面地实现 Auth0。我花了一点时间来弄清楚在我的应用程序中实现它的步骤，我希望它能节省您的时间！