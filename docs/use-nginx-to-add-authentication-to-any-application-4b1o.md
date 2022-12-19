# 使用 nginx 向任何应用程序添加身份验证

> 原文：<https://dev.to/oktadev/use-nginx-to-add-authentication-to-any-application-4b1o>

有没有发现自己想在登录表单后面放一个应用程序，但又害怕编写所有代码来处理 OAuth 2.0 或密码？在本教程中，我将向您展示如何使用 nginx `auth_request`模块来保护运行在 OAuth 2.0 的 nginx 服务器后面的任何应用程序，而无需编写任何代码！Lasso 是一个用 Go 编写的微服务，它将 OAuth 舞蹈处理给任意数量的不同身份验证提供者，所以你不必这么做。

## 为什么要在 Web 服务器上认证？

假设您使用 nginx 为您的团队运行一个小型的私有 wiki。首先，您可能会从为每个人添加一个 wiki 用户帐户开始。还不算太差，给新员工添加新账号，等他们走了再移除。

几个月后，随着您的团队和公司开始成长，您添加了一些服务器监控软件，并且希望将其放在登录之后，以便只有您的公司可以查看。由于它不是非常复杂的软件，最简单的方法是在一个`.htpasswd`文件中为每个人创建一个单一的密码，并与办公室共享该用户。

又过了一个月，您添加了一个持续集成系统，并把 GitHub 身份验证作为一个选项，这似乎是合理的，因为您的团队中的大多数人已经有了 GitHub 帐户。

此时，当有人新加入时，您必须为他们创建一个 wiki 帐户，将他们添加到 GitHub 组织，并为他们提供另一个系统的共享密码。当有人离开时，你可以删除他们的 wiki 帐户，并将其从 GitHub 中移除，但让我们面对现实吧，你可能暂时不会更改共享密码，因为不得不再次将该密码分发给每个人很烦人。

肯定有更好的方法来集成所有这些系统，使用一个公共的共享登录系统！问题是 wiki 是用 PHP 编写的，服务器监控系统最终只是发布了一个静态 HTML 的文件夹，而 CI 系统是用 Ruby 编写的，只有您团队中的一个人觉得这样写很舒服。

如果 web 服务器能够处理用户认证，那么每个后端系统都不需要担心这个问题，因为唯一能够通过的请求已经被认证了！

## 使用 nginx auth_request 模块

进入 nginx `auth_request`模块。

这个模块是 nginx 附带的，但是在编译 nginx 时需要启用。当您下载 nginx 源代码并编译时，只需将`--with-http_auth_request_module`标志和您使用的任何其他标志包含在内。

`auth_request`模块位于互联网和 nginx 向其传递请求的后端服务器之间，每当有请求进来时，它首先将请求转发到一个单独的服务器，以检查用户是否经过身份验证，并使用 HTTP 响应来决定是否允许请求继续到后端。

[![Flowchart illustrating the nginx auth_request module](../Images/47a2237f72821b809e52e5ab76c1f726.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Aq6FQAFd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/nginx-auth-request/nginx_auth_request-084b8c2075a5fe1d45ca2ab2d6438b70902f3d5da95b7d9e67c4e060b1ffb7c9.png)

这个图展示了一个对服务器名`stats.avocado.lol`的请求。首先，nginx 向`login.avocado.lol` (1)发出一个子请求，如果对该请求的响应(2)返回 HTTP 200，它就继续将请求转发到后端`stats.avocado.lol.`

## 选择授权代理

由于 nginx `auth_request`模块没有用户的概念，也不知道如何对任何人进行身份验证，所以我们需要在混合中添加一些东西来真正处理用户登录。在上图中，这由服务器名`login.avocado.lol`来说明。

该服务器需要处理一个 HTTP 请求，并根据用户是否登录返回 HTTP 200 或 401。如果用户没有登录，它需要知道如何让他们登录并设置一个会话 cookie。

为此，我们将使用开源项目“ [Lasso](https://github.com/LassoProject/Lasso) ”。Lasso 是用 Go 写的，部署起来超级简单。一切都可以通过一个 YAML 文件进行配置。Lasso 可以配置为通过各种 OAuth 和 OpenID 连接后端(如 GitHub、Google、Okta 或任何其他定制服务器)对用户进行身份验证。

我们将在几分钟后回来配置 Lasso，但现在，让我们继续在 nginx 中设置您的受保护服务器。

## 配置您受保护的 nginx 主机

从一个典型的 nginx 服务器块开始，您只需要添加几行代码来启用`auth_request`模块。下面是一个示例服务器块，看起来应该类似于您自己的配置。这个例子只提供了一个静态 HTML 文件的文件夹，但是无论是将请求传递给 fastcgi 后端还是使用`proxy_pass`，同样的想法也适用。

```
server {
  listen 443 ssl http2;
  server_name stats.avocado.lol;

  ssl_certificate /etc/letsencrypt/live/avocado.lol/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/avocado.lol/privkey.pem;

  root /web/sites/stats.avocado.lol;

  index index.html;
} 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容添加到现有的`server`块中:

```
# Any request to this server will first be sent to this URL
auth_request /lasso-validate;

location = /lasso-validate {
    # This address is where Lasso will be listening on
proxy_pass http://127.0.0.1:9090/validate;
proxy_pass_request_body off; # no need to send the POST body

proxy_set_header Content-Length "";
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;

# these return values are passed to the @error401 call
auth_request_set $auth_resp_jwt $upstream_http_x_lasso_jwt;
auth_request_set $auth_resp_err $upstream_http_x_lasso_err;
auth_request_set $auth_resp_failcount $upstream_http_x_lasso_failcount;
}

error_page 401 = @error401;

# If the user is not logged in, redirect them to Lasso's login URL
location @error401 {
return 302 https://login.avocado.lol/login?url=https://$http_host$request_uri&lasso-failcount=$auth_resp_failcount&X-Lasso-Token=$auth_resp_jwt&error=$auth_resp_err;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看这是怎么回事。第一行，`auth_request /lasso-validate;`是实现这种流动的原因。这告诉`auth_request`模块在决定是否允许继续到后端服务器之前，首先发送任何请求到这个 URL。

块`location = /lasso-validate`捕获该 URL，并将其代理给 Lasso 服务器，该服务器将监听端口 9090。我们不需要将 POST 主体发送给 Lasso，因为我们真正关心的是 cookie。

第`error_page 401 = @error401;`行告诉 nginx 如果 Lasso 返回一个 HTTP 401 响应该怎么做，就是把它传递给由`location @error401`定义的块。该块将用户的浏览器重定向到 Lasso 的登录 URL，这将启动流向真正的认证后端。

## 为 Lasso 配置一个服务器块

接下来，为 Lasso 配置一个新的服务器块，这样它就有一个类似于`https://login.avocado.lol`的可公开访问的 URL。所有这些需要做的就是将请求代理到后端 Lasso 服务器。

```
server {
  listen 443 ssl;
  server_name login.avocado.lol;

  ssl_certificate /etc/letsencrypt/live/login.avocado.lol/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/login.avocado.lol/privkey.pem;

  # Proxy to your Lasso instance
  location / {
    proxy_set_header Host login.avocado.lol;
    proxy_set_header X-Forwarded-Proto https;
    proxy_pass http://127.0.0.1:9090;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 配置和部署套索

你需要下载[套索](https://github.com/LassoProject/lasso)并为你的平台编译 Go 二进制文件。您可以按照项目自述文件中的说明进行操作。

一旦获得了二进制文件，就需要创建配置文件来定义 Lasso 认证用户的方式。

将`config/config.yml_example`复制到`config/config.yml`并通读那里的设置。大多数默认设置都可以，但是您可能想要创建自己的 JWT 秘密字符串并替换占位符值`your_random_string`。

配置 Lasso 最简单的方法是让它允许任何可以在 OAuth 服务器上验证的用户访问后端。如果您使用像 Okta 这样的私有 OAuth 服务器来管理您的用户，这非常有用。继续设置`allowAllUsers: true`来启用这个行为，并注释掉`domains:`块。

您需要选择一个 OAuth 2.0 提供者来实际验证用户。在这个例子中，我们将使用 Okta，因为这是拥有一个完整的 OAuth/OpenID 连接服务器并能够从一个仪表板管理所有用户帐户的最简单的方法。在你完成配置文件的填写之前，你需要在 developer.okta.com/注册一个 Okta 开发者账户。创建帐户后，点击顶部菜单中的**应用**，创建一个新的应用。选择 **Web** 作为应用平台。

[![Create a web application with Okta](../Images/fdd2f32f0c1669eef5634d4bf97bd2ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CGauv1yt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/nginx-auth-request/okta-create-app-a16ef6d91a90289f62f0b1ccd955b5e86e215d31c56e8be536485055d194eb86.png)

在下一个屏幕中，您需要配置**基本 URI** 和**登录重定向 URI** ，以匹配您自己的服务器设置。Lasso 的重定向 URI 以`/auth`结束，所以你的配置应该看起来像下面的截图。

[![Create a web application with Okta](../Images/917385af651a4d9fe4286dbcd2c9498f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BiPZRkW2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/nginx-auth-request/okta-configure-app-20a27af52e6acb17ae07596c298cd28c6c95342684faf94c2b1dcf15e8c6bb37.png)

一旦你完成了，Okta 会给你一个客户端 ID 和密码，你需要把它包含在配置文件中。

`config.yml`

```
oauth:
  provider: oidc
  client_id: {yourClientID}
  client_secret: {yourClientSecret}
  auth_url: https://{yourOktaDomain}/oauth2/default/v1/authorize
  token_url: https://{yourOktaDomain}/oauth2/default/v1/token
  user_info_url: https://{yourOktaDomain}/oauth2/default/v1/userinfo
  scopes:
    - openid
    - email
  # Set the callback URL to the domain that Lasso is running on
  callback_url: https://login.avocado.lol/auth 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以跑套索了！它将监听端口 9090，这是您配置 nginx 发送`auth_request`验证以及服务来自`login.avocado.lol`的流量的地方。

当您重新加载 nginx 配置时，所有对`stats.avocado.lol`的请求都需要您首先通过 Okta 登录！

## 奖金:谁登录了？

如果你在 nginx 后面放了一个动态 web 应用，你不仅关心*某人是否能够登录*，还关心*他们是谁*，那么我们还可以使用一个技巧。

默认情况下，Lasso 将通过 OpenID Connect(或者 GitHub 或 Google，如果您已经将它们配置为您的身份验证提供者)提取一个用户 ID，并将该用户 ID 包含在一个 HTTP 头中，该头被传递回主服务器。

在您的主服务器块中，就在启用`auth_request`模块的行`auth_request /lasso-validate;`的下面，添加以下代码:

```
auth_request_set $auth_user $upstream_http_x_lasso_user; 
```

Enter fullscreen mode Exit fullscreen mode

这将获取 Lasso 设置的 HTTP 头`X-Lasso-User`，并将其分配给 nginx 变量`$auth_user`。然后，根据您使用的是 fastcgi 还是 proxy_pass，在您的服务器块中包含下面两行中的一行:

```
fastcgi_param REMOTE_USER $auth_user;
proxy_set_header Remote-User $auth_user; 
```

Enter fullscreen mode Exit fullscreen mode

这些将设置一个值为`$auth_user`的 HTTP 头，您的后端服务器可以读取这个头，以便知道谁登录了。例如，在 PHP 中，您可以使用
来访问这些数据

```
<?php
echo 'Hello, ' . $_SERVER['REMOTE_USER'] . '!'; 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以确定您的内部应用程序只能由经过身份验证的用户访问！

## 了解有关 OAuth 2.0 和 Okta 安全用户管理的更多信息

有关 OAuth 2.0 的更多信息和教程，请查看我们的其他博客帖子！

*   [在 5 分钟内为你的 PHP 应用添加认证](https://dev.to/blog/2018/07/09/five-minute-php-app-auth)
*   [什么是 OAuth 2.0 授权码授予类型？](https://dev.to/blog/2018/04/10/oauth-authorization-code-grant-type)
*   什么是 OAuth 2.0 隐式授权类型？

一如既往，我们希望听到你对这篇文章的看法，或者任何其他的东西！请在评论中或在 Twitter 上联系我们！