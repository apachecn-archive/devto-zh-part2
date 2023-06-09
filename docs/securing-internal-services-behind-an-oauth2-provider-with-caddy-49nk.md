# 使用 Caddy 保护 OAuth2 提供者背后的内部服务

> 原文：<https://dev.to/kamaln7/securing-internal-services-behind-an-oauth2-provider-with-caddy-49nk>

<small>最后更新于 2019 年 3 月 1 日</small>

在下面的视频中，你可以看到`secret.int.localtest.me`在 Google OAuth2 登录之后是安全的。未经身份验证的请求被重定向到登录门户`auth.int.localtest.me`，该门户要求用户使用 Google 登录。有了这个，我们就可以对`int.localtest.me`下的所有站点进行一次认证。

[https://www.youtube.com/embed/EMr-eLJdYhE](https://www.youtube.com/embed/EMr-eLJdYhE)

`localtest.me`及其任何子域解析为`127.0.0.1`。这使得测试本地服务更加容易。这仅与视频演示相关。

## 🤔这是如何工作的？

在后台，使用 JWT 令牌处理身份验证，这些令牌在`int.localtest.me`主机名上设置为 cookies，使它们对所有子域可用。对经过身份验证的 URL 的每个请求都会检查 JWT 令牌。这是由[插件](https://caddyserver.com/docs/http.jwt)处理的。

登录门户使用 [`http.loginsrv`插件](https://caddyserver.com/docs/http.login)提供服务，该插件是绑定到 [tarent/loginsrv](https://github.com/tarent/loginsrv) 的助手。

## 🛠我该如何设置这个？

ℹ️**2019 年 3 月 1 日更新**由于 Google+被弃用，不再需要启用其 API 和 OAuth 范围。我已经删除了下面相关的部分。

我们将假设如下:

*   所有内部服务都托管在`*.int.domain.tld`
*   登录门户将在`login.int.domain.tld`提供服务
*   OAuth2 提供者是 Google，它有一个批准的电子邮件地址列表
*   Caddy 用作所有内部服务的前端代理或 web 服务器

### 🔓基础不安全环境

我们将从公开的内部服务开始:

`Caddyfile`

```
secret.int.domain.tld {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

### 📸安装 Caddy 插件

👋🏼**我更喜欢在 Docker 容器中运行 Caddy。但是，你不必！**您可以跳过这一部分，但是要确保您的 Caddy 二进制文件已经编译了`http.jwt`和`http.login`插件。你可以自己编译二进制文件，或者从[球童网站](https://caddyserver.com/download)下载一个预建的二进制文件，确保包含我提到的插件。

`abiosoft/caddy`图像不包括`http.jwt`和`http.login`插件，所以我们需要建立自己的图像。为此，运行:

```
docker build -t caddy-jwt-login --build-arg plugins="jwt,login" github.com/abiosoft/caddy-docker.git 
```

Enter fullscreen mode Exit fullscreen mode

如果您在同一个 Caddy 实例上托管其他站点，请确保包含您可能需要的任何其他插件。如果对您有意义，用不同的标签/图像名称替换`caddy-jwt-login`。

这将是我们的 Caddy 容器所基于的图像，而不是`abiosoft/caddy`。

🌟我已经创建并发布了一个您可以使用的图像，但我不能保证它总是最新的！`kamaln7/caddy-jwt-login`。截至目前，最新版本是`kamaln7/caddy-jwt-login:0.11.0`。

### 🔑设置 Google OAuth2 提供程序

为了能够使用 Google 作为 OAuth2 提供者，我们需要在开发人员控制台中创建一个项目，并向其中添加一个 OAuth2 服务。首先在这里创建一个新项目[。](https://console.developers.google.com/projectcreate)

然后，浏览到[开发者控制台](https://console.developers.google.com/apis/dashboard)。确定您的项目在左上角被选定。

转到[凭证页面](https://console.developers.google.com/apis/credentials)，可从左侧边栏访问。单击“Oauth 同意屏幕”选项卡，并填写“应用程序名称”。

向下滚动到“授权域”，并在字段中输入您的顶级域名(`domain.tld`)。保存并返回到“身份证明”页面。

在 Credentials 选项卡中，单击蓝色的 Create credentials 按钮并选择 OAuth client ID。

[![screenshot](img/b42e09abe34174ec5a3a53e6d84a5e86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fO71eJcS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kmln.sr/cepfXYu)

应用程序类型将是 Web 应用程序。随你怎么命名，将`https://auth.int.domain.tld/login/google`添加到授权重定向 URIs 列表。**填写完字段后，不要立即点击创建。**单击页面上的任何其他位置将其添加到列表中，然后单击创建。愚蠢的 UX，我知道。但这是必要的。

[![screenshot](img/d83ce02f9f318d5f525e5376c1a7c26e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pcl0okKT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kmln.sr/CcTZauP)

在接下来的页面上，您将看到一个弹出窗口，显示您的客户端 ID 和密码。把它们保存起来，因为我们以后会需要它们。

### 🌐创建登录门户

将以下内容添加到您的 Caddy 配置中。将`YOURCLIENTID`替换为您的客户端 ID，将`YOURCLIENTSECRET`替换为密码。

```
auth.int.domain.tld {
        tls your@email.address
        redir 302 {
                if {path} is /
                / /login
        }

        login {
                google client_id=YOURCLIENTID,client_secret=YOURCLIENTSECRET
                redirect_check_referer false
                redirect_host_file /redirect_hosts.txt
                cookie_domain int.domain.tld
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将使用您刚刚创建的 Google OAuth2 提供程序配置`http.login`插件，在`int.domain.tld`而不是`auth.int.domain.tld`上设置 cookies，并允许在`redirect_hosts.txt`文件中重定向到主机。更多关于下面的重定向。

✅:现在，当你浏览到`https://auth.int.domain.tld`，你就可以用你的谷歌账户登录了。请注意，*任何人*都可以在这里登录，但不是每个人都可以访问受保护的服务。我们将在下一节中限制对特定电子邮件地址的服务访问。

### 🔐保护内部服务

剩下的就是使用`http.jwt`插件来保护内部服务。我们将把这个配置提取到它自己的代码片段中，这样我们就可以很容易地在其他服务中重用它。

`Caddyfile`

```
(int-auth) {
        jwt {
                path /
                redirect https://auth.int.domain.tld/login?backTo=https%3A%2F%2F{host}{rewrite_uri_escaped}
                allow sub your@email.address
                allow sub otherpeson@gmail.com
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将在`/`(每个请求)上启用 JWT 认证，并将未经认证的请求重定向到我们的登录门户。`backTo`参数指示登录门户在成功登录时重定向回内部服务。对您希望允许访问的每个电子邮件地址重复`allow sub ...`指令。

💭还记得我们谈过的一个`redirect_hosts.txt`文件吗？这就是它的用武之地。该文件将包含允许登录门户重定向回的主机列表。默认情况下，它不允许`auth.int.domain.tld`之外的任何外部 URL。通过设置`redirect_check_referer false`并提供一个批准的主机列表，我们能够将用户重定向回我们的内部服务。

为每个内部服务创建一个文件，并将其添加到 Docker 映像或 Caddy 正在运行的任何地方。只要确保在上面的 Caddy 配置中更新它的路径就行了(`login {}`块)。

`redirect_hosts.txt`

```
secret.int.domain.tld
another-service.int.domain.tld 
```

Enter fullscreen mode Exit fullscreen mode

✨最后，将这个代码片段导入内部服务配置:

`Caddyfile`

```
secret.int.domain.tld {
    import int-auth
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

### 现在怎么办？

就是这样！您的内部服务通过登录门户得到保护。

#### 可选的跨 Caddy 重启的⚡会话持久性

JWT 令牌使用秘密进行签名。这允许服务器验证令牌没有被篡改(被任何无权访问机密的人篡改)。`http.login` [寻找存储在`JWT_SECRET`环境变量](https://github.com/tarent/loginsrv/blob/e482109fc416fb8678befb27336ab5d08ff10252/caddy/setup.go#L51-L55)中的秘密:

*   如果它存在，它就使用它
*   否则，它使用`loginsrv`的默认秘密并更新环境变量。默认的秘密是一个[随机生成的字符串](https://github.com/tarent/loginsrv/blob/master/login/config.go#L20)

在验证请求时，`http.jwt`使用`JWT_SECRET`环境变量。这一切都很好，因为默认密码是在启动时随机生成的，而不是硬编码的字符串。但是，这意味着当 Caddy 重新启动时，密码会发生变化，这会导致所有用户被注销，因为他们的令牌会失效。

为了使会话在重启后持续，我们需要设置我们自己的固定秘密。你可以在 random.org 上随机生成两个 16 个字符长的字符串[，并将它们放在一起作为一个 32 个字符长的秘密。(您需要一个付费的 random.org 帐户来生成超过 20 个字符的字符串)。将环境变量`JWT_SECRET`设置为这个令牌，并使其对 Caddy 可用。](https://www.random.org/strings/?num=2&len=16&digits=on&upperalpha=on&loweralpha=on&unique=on&format=html&rnd=new)

# 📚资源

*   [tart/loginsrv](https://github.com/tarent/loginsrv)
*   [http.login](https://caddyserver.com/docs/http.login) - Caddy 插件
*   [http.jwt](https://caddyserver.com/docs/http.jwt) -球童插件
*   [loginsrv ← OAuth2](https://github.com/tarent/loginsrv#oauth2)

* * *

我希望你们都觉得这有用！我个人喜欢凯蒂，我总是在寻找新的酷的方式来使用它。我总是很高兴在评论中听到任何形式的反馈😄

*本文最初发表于 [kamal.io](https://kamal.io/blog/securing-internal-services-behind-oauth2-with-caddy/) 。*