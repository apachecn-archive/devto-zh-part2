# 在 WhatsApp 上用 Rails、Twilio 和 Spotify 建立一个协作播放列表

> 原文：<https://dev.to/twilio/build-a-collaborative-playlist-over-whatsapp-with-rails-twilio-and-spotify-1fnn>

通过播放列表共享音乐是发现新旧音乐的好方法。Spotify 有合作播放列表，但我不喜欢他们让你的朋友重新排序和从列表中删除歌曲的方式。我们可以通过构建自己的协作播放列表来解决这个问题，该列表只允许使用 [Spotify Web API](https://developer.spotify.com/documentation/web-api/) 添加内容。有了用于 WhatsApp 的[Twilio API](https://www.twilio.com/whatsapp)，我们可以让我们的朋友在有灵感时发送一首歌曲。

在这篇文章中，我们将使用 Ruby on Rails 构建一个 WhatsApp 机器人来完成上述所有工作。

## 入门

要构建这个应用程序，我们需要一些东西:

*   [安装了 Ruby 版本 2 . 5 . 1](https://www.ruby-lang.org/en/downloads/)
*   轨道和捆扎机，可安装`gem install rails bundler`
*   Twilio 帐户([在此注册免费的 Twilio 帐户](https://www.twilio.com/try-twilio)
*   在我们的 Twilio 帐户中设置的 WhatsApp 沙盒
*   [一个 Spotify 开发者账户](https://developer.spotify.com/)
*   [ngrok](https://ngrok.com) ，这样我们就可以[通过隧道到达我们本地开发的应用程序来使用 webhooks](https://www.twilio.com/blog/2015/09/6-awesome-reasons-to-use-ngrok-when-testing-webhooks.html)

### Spotify API 凭证

要使用 Spotify API，我们需要生成一些 API 凭证。从 [Spotify 开发者仪表盘](https://developer.spotify.com/dashboard/applications)创建一个新应用程序，并遵循 3 步流程。

1.  首先，填写一些关于应用程序的详细信息。说出它的名字，我把我的叫做 WhatsPlaying，提供一个简短的描述并检查它是什么类型的应用程序，我选择了“网站”
2.  接下来我们被问及这是否是商业整合；选择“否”
3.  最后，检查协议

完成这些步骤后，我们将看到应用程序控制面板。我们需要从这个页面的客户端 ID 和客户端密码。

这里我们还需要做一件事。点击“编辑设置”，输入重定向网址`http://localhost:3000/auth/spotify/callback`，点击“添加”。

既然凭证已经排序，我们就可以开始构建我们的应用程序了。

## 准备项目

我已经开始了这个项目，所以[从 GitHub](https://github.com/philnash/whats_playing) 克隆或下载回购协议，并检查`getting-started`分支。

```
git clone -b getting-started https://github.com/philnash/whats_playing.git 
```

Enter fullscreen mode Exit fullscreen mode

切换到目录并安装项目的依赖项。

```
cd whats_playing
bundle install 
```

Enter fullscreen mode Exit fullscreen mode

将`config/env.yml.example`文件复制到`config/env.yml`(我们使用 envyable 来[管理应用程序](https://www.twilio.com/blog/2015/02/managing-development-environment-variables-across-multiple-ruby-applications.html)的环境变量)。打开`config/env.yml`，填写我们刚刚生成的 Spotify 客户端 ID 和客户端密码。我们很快会把剩下的补上。

我们现在将为您在 Spotify 上的帐户生成一个 Spotify 访问令牌，以及一个名为“WhatsPlaying”的新播放列表，我们将把它用作我们的协作播放列表。使用
运行 Rails 应用程序

```
bundle exec rails server 
```

Enter fullscreen mode Exit fullscreen mode

在[打开 app http://localhost:3000](http://localhost:3000)。会有一个“登录 Spotify”的链接。单击链接并遵循 OAuth 流程。所有这些都是由 [RSpotify gem](https://github.com/guilhermesad/rspotify) 和 [omniauth](https://github.com/omniauth/omniauth) 处理的，你可以在`config/initializers/rspotify.rb`中查看完成这些工作所需的配置。

[![](img/931f10c15a285f2edf8f0e2a20a12ec7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---ulZ09x4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/-cLIvjXZ1zUy1dnYRd6rWFkXIhh0AO33g1YJCEHZAb_Q6x.width-500.png)

一旦您被引导回应用程序，您将在您面前的页面上看到您的 Spotify 用户 ID、访问令牌、刷新令牌和播放列表 ID。将这些复制到`config/env.yml`中，重启应用程序，再次打开 [http://localhost:3000](http://localhost:3000) 。您会发现脚本刚刚创建的播放列表是空的，并嵌入在页面中。现在是时候在 WhatsApp、Twilio 和 Spotify 之间建立整合了。

## 用网钩抓牢

当你向你用 Twilio 控制的 WhatsApp 号码发送消息时，Twilio 会向我们提供的应用程序 URL 发送一个 HTTP 请求或 webhook。我们将用它来回复收到的消息，并将歌曲添加到我们的播放列表中。让我们首先设置我们的 webhook 端点。

为此，我们将安装 [twilio-ruby 宝石](https://github.com/twilio/twilio-ruby/)。这将使编写 TwiML(Twilio 理解并接受其指导的 XML)变得更加容易。打开`Gemfile`，在包含 RSpotify 的行下，添加 twilio-ruby:

```
gem 'rspotify', '~> 2.1.1'
gem 'twilio-ruby', '~> 5.12.3' 
```

Enter fullscreen mode Exit fullscreen mode

通过再次运行项目目录中的`bundle install`来安装 gem。

我们现在需要一个新的控制器来处理传入的 webhooks。使用 Rails 生成器创建一个新的:

```
bundle exec rails generate controller twilio/messages 
```

Enter fullscreen mode Exit fullscreen mode

打开新生成的`app/controllers/twilio/messages_controller.rb`，开始移除默认的[轨道 CSRF 保护](https://guides.rubyonrails.org/security.html#csrf-countermeasures)。根据定义，Webhooks 是一个跨站点请求，我们应该通过[验证请求来自 Twilio](https://www.twilio.com/docs/usage/security) 来保护它们。我们可以稍后使用 twilio-ruby gem 提供的 [Twilio 请求验证框架中间件来实现。](https://www.twilio.com/blog/2014/09/securing-your-ruby-webhooks-with-rack-middleware.html) 

```
class Twilio::MessagesController < ApplicationController
  skip_before_action :verify_authenticity_token
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要一个动作来处理传入的 webhook 请求。在 Rails 的上下文中，我认为 webhooks 是`create`动作，所以创建一个新的`create`方法。

```
class Twilio::MessagesController < ApplicationController
  skip_before_action :verify_authenticity_token
  def create
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

为了确保我们在正确的轨道上，让我们首先用一个简单的消息进行响应。我们将使用 twilio-ruby gem 的 TwiML 生成功能，用习惯的“Hello World！”来响应任何传入的消息。

```
class Twilio::MessagesController < ApplicationController
  skip_before_action :verify_authenticity_token
  def create
    response = Twilio::TwiML::MessagingResponse.new
    response.message(body: "Hello World!")
    render xml: response.to_xml
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个动作也需要一个路线。打开`config/routes.rb`并添加以下内容:

```
Rails.application.routes.draw do
  namespace :twilio do
    post 'messages', to: 'messages#create'
  end
  # other routes
end 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们需要得到响应的全部内容，所以让我们将 Twilio WhatsApp 沙箱连接到这个应用程序。

启动你的应用:

```
bundle exec rails server 
```

Enter fullscreen mode Exit fullscreen mode

启动 ngrok 隧道，以便 Twilio webhook 可以通过公共 URL 访问您的应用程序。在新的终端窗口命令行上运行:

```
ngrok http 3000 
```

Enter fullscreen mode Exit fullscreen mode

获取 ngrok 生成的 URL，添加`/twilio/messages`路径，并将其作为 [WhatsApp 频道](https://www.twilio.com/console/sms/whatsapp/sandbox)的“有消息进来”时的 URL。

[![](img/8fe23402ae5798cda39728b0873e4265.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lGhTdgqI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/mOcd_C7CBBLk1HcW4JB11qrpaKD6I0_o2vJlWe4cZDtoYi.width-500.png)

向 WhatsApp 沙盒号码发送一条消息，你会得到一个回复，说“你好，世界！”。现在是时候连接 Spotify API 了。

## 与 Spotify API 连接

我们将构建一个对话界面，与 Spotify API 进行交互。我们将假设当用户发送他们的第一条消息时，是他们想要添加到播放列表中的歌曲标题。我们将使用 Spotify API 搜索该歌曲，并将第一个结果发送给用户，看看这是否是他们想要的。如果他们以肯定的回应确认，那么我们会将该曲目添加到播放列表中。如果他们说不，我们将要求他们再次搜索。如果他们发回任何东西，我们可以认为这是一次新的搜索。

我们为这个应用程序使用了优秀的 [RSpotify](https://github.com/guilhermesad/rspotify) 库，但是让我们创建一个更适合我们使用的类。在`app`目录中创建一个名为`services`的新目录，并在其中创建一个名为`spotify.rb`的文件。打开`spotify.rb`并创建一个新类:

```
class Spotify
end 
```

Enter fullscreen mode Exit fullscreen mode

在初始化器中，我们将使用之前创建的凭证来创建一个经过身份验证的用户。

```
class Spotify
  def initialize(user_id, user_token, user_refresh_token)
    @user = RSpotify::User.new({
      'id' => user_id,
      'credentials' => {
        'token' => user_token,
        'refresh_token' => user_refresh_token
      }
    })
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

通过向`RSpotify::Track.search`方法传递一个查询来搜索音轨，我们只想返回第一个结果。

```
class Spotify
  def initialize(user_id, user_token, user_refresh_token)
  end

  def track_search(query)
    RSpotify::Track.search(query).first
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过从`track_search`的结果中获取曲目 ID，通过 ID 搜索播放列表，然后将曲目添加到播放列表中，从而将曲目添加到播放列表中。

```
class Spotify
  def initialize(user_id, user_token, user_refresh_token)
  end

  def track_search(query)
  end

  def add_to_playlist(playlist_id, track_id)
    playlist = RSpotify::Playlist.find(@user.id, playlist_id)
    playlist.add_tracks!([track_id])
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们在控制器中使用这个类，再次打开`app/controllers/twilio/messages_controller.rb`。在`create`方法的开始，获取消息的正文，我们将把它用作搜索词，并使用环境中的凭证实例化一个新的 Spotify 对象。

### 搜索歌曲

我们将首先测试搜索功能，并返回我们找到的第一首曲目的名称和艺术家。Spotify 以列表形式返回艺术家，因为可能有多个艺术家负责一首曲目。我们可以用一点积极支持的技巧把它变成一个句子。 [WhatsApp 还支持一些基本的消息格式化](https://faq.whatsapp.com/en/android/26000002/)；如果我们用下划线将曲目和艺术家的名字括起来，它们将以斜体显示。

```
class Twilio::MessagesController < ApplicationController
  skip_before_action :verify_authenticity_token
  def create
    body = params['Body']
    spotify = Spotify.new(ENV['SPOTIFY_USER_ID'], ENV['SPOTIFY_TOKEN'], ENV['SPOTIFY_REFRESH_TOKEN'])

    track = spotify.track_search(body)
    message = "Did you want to add _#{track.name}_ by _#{track.artists.map(&:name).to_sentence}_?"

    response = Twilio::TwiML::MessagingResponse.new
    response.message(body: message)
    render xml: response.to_xml
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

重新启动应用程序并发送信息，搜索您想听的曲目。您应该会收到一条列出您想要的曲目的消息。如果您获得不同的歌曲，请尝试添加艺术家或专辑以缩小搜索范围。

[![](img/41514bd0be989a856affab9ce481cbbb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ycyWCE3_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/bA3Sdd8dpQp03o8M8PfVIFv5Hm-L19JSBaeuItZpblztY0.width-500.png)

如果我们找不到任何歌曲，我们确实需要处理这个案例。

```
 def create
    body = params['Body']
    spotify = Spotify.new(ENV['SPOTIFY_USER_ID'], ENV['SPOTIFY_TOKEN'], ENV['SPOTIFY_REFRESH_TOKEN'])

    track = spotify.track_search(query)
    if track
      message = "Did you want to add _#{track.name}_ by _#{track.artists.map(&:name).to_sentence}_?"
    else
      message = "I couldn't find any songs by searching for '#{body}'. Try something else."
    end

    response = Twilio::TwiML::MessagingResponse.new
    response.message(body: message)
    render xml: response.to_xml
  end 
```

Enter fullscreen mode Exit fullscreen mode

### 添加到播放列表

我们可以处理搜索请求。现在，我们需要能够添加轨道，如果用户的回答是肯定的。

像 Twilio 上的 SMS 对话一样，我们可以使用会话来保留消息之间的数据。对于此应用程序，我们可以在会话中存储曲目 URI，以便 Spotify 识别它。然后，如果我们在会话中有一个跟踪，我们可以在响应中寻找“是”。如果我们得到“是”，我们将曲目添加到播放列表并清除会话。

```
 def create
    body = params['Body']
    spotify = Spotify.new(ENV['SPOTIFY_USER_ID'], ENV['SPOTIFY_TOKEN'], ENV['SPOTIFY_REFRESH_TOKEN'])

    if session[:track]
      answer = body.split(' ').first.downcase.strip
      if ['yes', 'yeah', 'yep', 'yup', '👍'].include? answer
        message = "OK, adding that track now."
        spotify.add_to_playlist(ENV['SPOTIFY_PLAYLIST_ID'], session[:track])
        session[:track] = nil
      end
    end

    if !message
      track = spotify.track_search(body)
      if track
        session[:track] = track.uri
        message = "Did you want to add _#{track.name}_ by _#{track.artists.map(&:name).to_sentence}_?"
      else
        message = "I couldn't find any songs by searching for '#{body}'. Try something else."
      end
    end
    response = Twilio::TwiML::MessagingResponse.new
    response.message(body: message)
    render xml: response.to_xml
  end 
```

Enter fullscreen mode Exit fullscreen mode

我添加了一些其他说“是”的选项，包括竖起大拇指的表情符号👍。[谁不爱表情符号？](https://www.twilio.com/blog/2018/08/emoji-translations-twilio-api-whatsapp-node-js.html)

你可以通过应用程序测试这条路径，方法是向 WhatsApp 号码发送一首歌曲，然后用“是”进行跟进。然后检查歌曲是否出现在播放列表中。

### 其他响应

为了完成我们的 Spotify WhatsApp 机器人，我们需要处理其他潜在的回应。如果用户发回否定的回答，那么我们想问他们想添加什么样的曲目。任何其他反应，我们会假设这是一个新的搜索。

```
 def create
    body = params['Body']
    spotify = Spotify.new(ENV['SPOTIFY_USER_ID'], ENV['SPOTIFY_TOKEN'], ENV['SPOTIFY_REFRESH_TOKEN'])

    if session[:track]
      answer = body.split(' ').first.downcase.strip
      if ['yes', 'yeah', 'yep', 'yup', '👍'].include? answer
        message = "OK, adding that track now."
        spotify.add_to_playlist(ENV['SPOTIFY_PLAYLIST_ID'], session[:track])
        session[:track] = nil
      elsif ['no', 'nah', 'nope', '👎'].include? answer
        session[:track] = nil
        message = "What do you want to add?"
      end
    end

    if !message
      track = spotify.track_search(body)
      if track
        session[:track] = track.uri
        message = "Did you want to add _#{track.name}_ by _#{track.artists.map(&:name).to_sentence}_?"
      else
        message = "I couldn't find any songs by searching for '#{body}'. Try something else."
      end
    end
    response = Twilio::TwiML::MessagingResponse.new
    response.message(body: message)
    render xml: response.to_xml
  end 
```

Enter fullscreen mode Exit fullscreen mode

再次重启应用程序，开始搜索并确认或拒绝曲目。你可以看到你添加的歌曲出现在你的播放列表中，也可以在你的应用主页上的嵌入式播放列表中看到。

## 一个机器人和一个全新的播放列表

通过 Spotify API 和 WhatsApp 的 Twilio API，我们刚刚建立了一个协作播放列表。现在交互非常简单，但你可以在此基础上增加对歌曲的投票，将它们在播放列表中上移，或者更好地与更多朋友分享播放列表。

如果你想看到这个应用程序的所有代码，请查看 GitHub 上的 repo。

我想看看你会在播放列表中添加什么会很有趣，所以我部署了这个应用程序的我的版本。你可以通过向这个 Twilio WhatsApp 号码+14155238886 发送消息“ **join beaver-wrasse** ”来连接到我的 WhatsApp 沙盒，从而向它添加歌曲。那就开始给我发你最喜欢的歌吧！点击这里查看[在线播放列表](https://twilio-whatsapp-spotify.herokuapp.com/)！

你有什么可以用 WhatsApp 和 Twilio 构建协作应用的想法吗？请在评论中、[在推特上](https://twitter.com/philnash)或通过电子邮件在 philnash@twilio.com[告诉我。](mailto:philnash@twilio.com)