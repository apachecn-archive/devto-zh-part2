# 用 Lucky 搜索 OAuth

> 原文：<https://dev.to/jwoertink/google-oauth-with-lucky-5c10>

本教程将向您展示如何通过用户的 google 帐户获取访问令牌。你可以用它来做一些事情，比如连接到 google docs，drive，analytics，以及很多其他的事情。也许您想制作一个在 google sheets 中存储数据的表单。或者你可能想建立你自己的自定义仪表板，显示你的网站的谷歌分析数据。无论是哪种情况，在用户通过 google 认证后，您都需要访问令牌。

要开始这个，我们只要用[水晶](https://crystal-lang.org/) 0.26.1 拉[幸运](https://luckyframework.org/)的主分支。因为这些东西一直在变化，所以当你读到这篇文章的时候，它可能已经过时了。不好意思！

如果您还没有安装 crystal，请查看[安装指南](https://crystal-lang.org/docs/installation/)。

## 第一步

我们需要幸运的 cli 工具。

```
git clone https://github.com/luckyframework/lucky_cli.git
cd lucky_cli
crystal build --release ./src/lucky.cr
mv ./lucky /usr/local/bin/ 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步

现在我们有了一个幸运的 cli 工具，我们可以生成幸运的应用程序。`cd`进入您选择的目录，然后...

```
lucky init
> Project name? google_auth
> API only or full support for HTML and Webpack? (api/full): full
> Generate authentication? (y/n): n
cd google_auth
./bin/setup
lucky dev 
```

Enter fullscreen mode Exit fullscreen mode

呜！如果浏览器窗口打开到 localhost:3001，并带有一个幸运的欢迎页面，那么一切都很好。如果没有，来和我们聊聊天，这样我们就能找出问题所在。

## 第三步

好了，现在你有了一个正在运行的应用程序，我们有了一个基线，以防从这一点开始有些东西不工作。随便用树枝什么的把这东西弄上去。

我们需要添加一个新的碎片来帮助我们完成大部分繁重的工作。将[多身份验证](https://github.com/msa7/multi_auth)碎片添加到您的`shard.yml`文件中。

```
dependencies:
#...
  multi_auth:
    github: msa7/multi_auth
    branch: master
# ... 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:在我最初写这个东西的时候，我发现了一个 [bug](https://github.com/msa7/multi_auth/pull/12) 。如果这个 PR 没有合并，你可能需要使用[我的叉子](https://github.com/jwoertink/multi_auth/tree/bugs/missing_hash_key)

既然已经添加了，那么一定要运行`shards install`来安装这个碎片。

## 第四步

是时候添加一些代码了！在您的`config/`目录
中创建这个文件

```
# config/multi_auth_handler.cr
require "multi_auth"

class MultiAuthHandler
  MultiAuth.config("google", ENV["GOOGLE_OAUTH_ID"], ENV["GOOGLE_OAUTH_SECRET"])

  def self.authorize_uri(provider : String)
    MultiAuth.make(provider, "#{Lucky::RouteHelper.settings.base_uri}/oauth/#{provider}/callback").authorize_uri(scope: "profile")
  end

  def self.user(provider : String, params : Enumerable({String, String}))
    MultiAuth.make(provider, "#{Lucky::RouteHelper.settings.base_uri}/oauth/#{provider}/callback").user(params)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个类设置你的多重验证配置，然后给你两个方便的方法。第一个`authorize_uri`生成用户被重定向到的 URL，在本例中是 google。第二个`user`是 google 返回的 OAuth 用户。

接下来，让我们制作一些页面。

编辑`src/actions/home/index.cr`文件。

```
class Home::Index < BrowserAction
  get "/" do
    token = session.get?(:token) || ""    # add this line
    render Home::IndexPage, token: token  # update this line
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这只是告诉 Lucky 使用您的自定义索引页面，而不是默认页面。

创建一个`src/pages/home/index_page.cr`文件。

```
class Home::IndexPage < MainLayout
  needs token : String

  def content
    h1 @token
    div do
      link "Login", to: OAuth::Handler.with("google")
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这是您闪亮的新主页，带有登录链接。

创建一个`src/actions/oauth/handler.cr`文件。

```
class OAuth::Handler < BrowserAction
  get "/oauth/:provider" do
    redirect to: MultiAuthHandler.authorize_uri(provider)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

该操作将用户重定向到 google 以选择他们的 google 帐户。

创建一个`src/actions/oauth/callback.cr`文件。

```
class OAuth::Callback < BrowserAction
  get "/oauth/:provider/callback" do
    user = MultiAuthHandler.user(provider, request.query_params)
    if user.access_token
      session.set(:token, user.access_token.as(OAuth2::AccessToken).access_token)
      flash.success = "It worked!"
    else
      flash.failure = "That did not work"
    end
    redirect to: Home::Index
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当用户离开谷歌，回到你的幸运应用时，这个动作被点击。我们传入 google 发送给我们的参数，然后获取用户信息。如果用户有一个`access_token`，那么我们将这个令牌设置到我们的用户会话中。`flash`将是显示给用户的消息，不管这是否有效。(希望有效)。最后，我们只是重定向到主页。

这实际上是我们需要做的所有代码。不过，我们遗漏了一些东西。因此，如果您试图在此时运行代码，您可能会看到一些异常爆发。

## 第五步

我们只是在开发中运行这些，但是对于开发，google 不会让你重定向到 localhost。不过没关系，我们可以很容易地绕过它。我们将“假装”我们的应用程序域实际上是`http://google-auth.lvh.me:3001`。`lvh.me`是一个特殊的域设置，用于指向您的本地计算机。好的事情是，它看起来像一个真正的域，给你使用子域的能力，你仍然可以访问你的开发网站，没有其他配置。不过，我们确实需要改变一件事。

打开`config/watch.yml`并更新到此

```
host: google-auth.lvh.me
port: 3001 
```

Enter fullscreen mode Exit fullscreen mode

回到我们添加的第一个文件，有一行代码`Lucky::RouteHelper.settings.base_uri`。这将指向本地开发中的这个监视文件。默认情况下应该是`0.0.0.0`，这通常是好的；然而，在这种情况下，我们构建一个 URL 来告诉 google 让我们返回。我们希望谷歌把我们送回`http://google-auth.lvh.me:3001/oauth/google/callback`。如果我们不更新这个 watch.yml，谷歌会试图把我们送回`http://0.0.0.0:5000/oauth/google/callback`，谷歌会对你大喊大叫。

最后，提到了两个环境变量`ENV["GOOGLE_OAUTH_ID"]`、`ENV["GOOGLE_OAUTH_SECRET"]`。我们需要实际获得这些。我要说这可能是整个事情中最困难的部分。

1.  注册你的应用-[https://console.developers.google.com/](https://console.developers.google.com/)
2.  生成 OAuth 凭证-[https://console.developers.google.com/apis/credentials](https://console.developers.google.com/apis/credentials)
3.  启用 Google People API-[https://console . developers . Google . com/APIs/API/People . Google APIs . com/overview](https://console.developers.google.com/apis/api/people.googleapis.com/overview)

如果你被困在这些步骤中，有大量不同的链接。建议谷歌搜索“谷歌 oauth 凭据”。

在凭证下设置好“OAuth 2.0 客户端 id”后，您需要更新*授权重定向 URIs* 部分并添加`http://google-auth.lvh.me:3001`。这将允许谷歌安全地将你重定向回你的幸运应用。

如果此时您已经有了 oauth 密钥，那么回到您的幸运代码，并在项目的根目录下添加一个新的`.env`文件。

```
GOOGLE_OAUTH_ID=your_oauth_client_id
GOOGLE_OAUTH_SECRET=your_oauth_client_secret 
```

Enter fullscreen mode Exit fullscreen mode

应该就是这样！确保你的应用程序已经启动，进入主页，点击“登录”。你将被带到谷歌选择你的帐户，并同意“谷歌认证应用程序想看看你的个人资料数据”。接受后，你将被重定向回你的幸运应用的主页，上面有一个巨大的 h1 标签，显示你的访问令牌。

既然你已经了解了基础知识，是时候构建你真正的应用程序了，并且让它变得漂亮和实用！