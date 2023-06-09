# 使用 Twilio 将短信通知添加到您的 Rails 应用程序

> 原文：<https://dev.to/yechielk/add-sms-notifications-to-your-rails-app-using-twilio-20kd>

任何住在纽约有车的人都知道交替靠边停车规则的痛苦。你需要时刻意识到你的车停在街道的哪一边，在最不合适的时候跑出去把车停在街道的另一边，偶尔还有被并排停车的人挡住去路的机会。所有这些，以及更多，都是纽约司机每周必须忍受的一部分。

<figure>[![cat napping on a car](img/19aeab932cdf8ab47df715996ff8c50c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QOQlURjN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AeiWfOVn6AlFhW_J1.png) 

<figcaption>你的意思是我得把这车搬走？</figcaption>

</figure>

为了帮助我解决这个问题，我创建了一个推特机器人 [@AlterSideBot](https://twitter.com/AlterSideBot) ，每当[备用路边停车规则](http://www1.nyc.gov/nyc-resources/service/1029/alternate-side-parking-or-street-cleaning)被暂停时，它就会转发微博，所以我知道我在那些日子里不必担心。

这个想法突然出现，追随者不断涌入…但它有其局限性。首先，大多数人并没有粘在他们的推特上，一条推特很容易淹没在噪音中。更不用说许多人甚至不使用 Twitter(或者很少使用)，所以我的机器人对他们没有任何帮助。

出于这个原因，我决定给我的 Twitter 机器人添加短信功能，这样人们就可以订阅接收手机短信通知。

这个应用非常成功；几天之内，我就有了 100 多个订户。我从中获得了很多乐趣，所以我想我应该写下这个过程。

出于本教程的考虑，我不会深入讨论 Twitter 机器人的制作(这本身就是一篇[完整的博客文章](https://blog.yechiel.me/creating-my-first-twitter-bot-b5e0da5c8cbb?gi=da4d1ec61053))，相反，我们将创建一个更简单的应用程序，人们可以在其中订阅接收猫的事实。

### 你需要什么

*   运行 MacOS 或 Linux 的电脑(如果你使用的是 Windows 10，你可以按照本指南安装 Rails)
*   [Rails 版本 5](http://rubyonrails.org/) 或更高版本
*   一个 Twilio 账户([你可以在这里](https://www.twilio.com/try-twilio)注册一个免费试用的账户)和一个可以发送/接收短信的电话号码
*   可以发送/接收短信的电话，以便您可以测试您的应用程序

### 构建 Rails Cat Facts app

#### 入门

让我们从创建 Rails 应用程序开始。在你的终端运行`rails new cat_facts`。这将为一个名为 Cat Facts 的基本 Rails 应用程序生成一个模板。当你的终端完成它正在做的事情时，输入`cd cat_facts`进入你的应用程序的根目录。

在你最喜欢的编辑器中打开新创建的`cat_facts`目录，让我们开始吧。

#### 给我事实

我们将从创建我们的`CatFact`模型开始，这是我们应用程序的神经引擎。

在你的终端运行`rails generate model cat_fact fact:string`。这将生成一个`CatFact`模型，该模型具有一个`fact`属性，我们将在其中存储我们的事实，以及一个类似如下的迁移:

```
class CreateCatFacts < ActiveRecord::Migration[5.1] 
    def change 
        create_table :cat_facts do |t| 
            t.string :fact 
            t.timestamps 
        end 
    end 
end 
```

仔细检查所有东西看起来都应该是这样，然后运行`rails db:migrate`来创建数据库表。

现在让我们通过运行`rails generate controller cat_facts index create`来生成控制器，这将生成一个带有`index`和`create`动作的`CatFactsController`。

控制器还将在`GET cat_facts/index`和`GET cat_facts/create`为我们的应用程序创建路线。这比我们需要的要复杂得多，让我们到`config/routes.rb`，用下面的内容替换那个文件的内容:

```
Rails.application.routes.draw do

    resources :cat_facts, only: [:create, :index]

end 
```

这将为我们提供一条在`GET /cat_facts`显示所有事实的路线，以及一条可以用来创建新的 Cat 事实的`POST /cat_facts`路线。

#### 有视图的 App

让我们现在建立我们的观点。将以下内容放入`app/views/cat_facts/index.html.erb` :

```
<h1>Cat Facts</h1>

<p>Add a new cat fact:</p>

<%= form_for @cat_fact do |f| %>

    <%= f.label :fact %><br>

    <%= f.text_area :fact %><br>

    <%= f.submit %>

<% end %> 
```

这将为我们提供一个创建新的 Cat 事实的表单。

在那下面放上以下内容:

```
<h3>Previous Facts:</h3>

<ul>

    <% @cat_facts.each do |cat_fact| %>

        <li><%= cat_fact.fact %></li>

    <% end %>

</ul> 
```

这将让我们看到我们创建的所有事实的列表。

现在让我们来构建我们的控制器。在`app/controllers/cat_facts_controller.rb`中编辑`index`方法，如下所示:

```
def index

    @cat_fact = CatFact.new

    @cat_facts = CatFact.all

end 
```

而`create`的方法为:

```
def create

    @cat_fact = CatFact.new

    @cat_fact.fact = params[:cat_fact][:fact]

    @cat_fact.save

    redirect_to cat_facts_path

end 
```

我们现在可以对此进行测试。在您的终端中运行`rails server`。打开浏览器，进入[http://localhost:3000/cat _ facts](http://localhost:3000/cat_facts)，您应该会看到一个新的 cat facts 表单。如果你输入一个事实并点击提交，你应该会看到它出现在表单下面。

[![sceenshot](img/c6b2aba3139650659e3b03da35bda0da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AVR3fiSI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/882/0%2AfmSCHcnB9l4tDmvH.png)

### 添加短信通知

现在我们已经有了应用程序的基础，我们可以将它连接到我们的 Twilio 帐户，让人们订阅接收通知。

#### 配置我们的 app

首先，让我们添加 twilio-ruby gem，这样我们的应用程序就可以与 twilio 对话。在您的 gem 文件中添加下面一行:`gem 'twilio-ruby'`，然后在您的终端中运行`bundle install`。

接下来，我们必须向我们的应用程序提供您的 Twilio 凭据。登录你的 Twilio 账户，进入[控制台](https://www.twilio.com/console)。在左上方，您将看到您的帐户 SID 和 Auth Token，记下它们用于下一步。

[![screenshot](img/d8a5fa85191be44feb9c5b042d567750.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2UbE6hDw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/775/0%2AEkdcU7QBj4YLQHis.png)

在你的应用程序中，在`config/initializers`中创建一个名为`twilio.rb`的文件，并粘贴以下代码:

```
Twilio.configure do |config|

    config.account_sid = "ACCOUNT_SID"

    config.auth_token = "AUTH_TOKEN"

end 
```

确保用您在上一步中保存的帐户 SID 和 Auth 令牌替换`ACCOUNT_SID`和`AUTH_TOKEN`(如果您计划将代码上传到 GitHub 等，请不要提交您的令牌和 SID，而是考虑使用环境变量)。

接下来，让我们为 Twilio 设置一条与我们的应用程序交互的路线。在您的终端键入:`rails generate controller twilio sms`。这将为我们的应用程序提供一条在`get '/twilio/sms'`的路线以及一个相应的`TwilioController`和一个`sms`动作。

在`config/routes.rb`让我们将`get '/twilio/sms'`路线改为`post '/twilio/sms'`。

#### 配置 Twilio

现在，我们需要告诉 Twilio 在哪里可以找到你的应用，但为了做到这一点，我们需要将我们的应用公开到互联网上。我们将使用 ngrok 来实现这一点。

要检查您的计算机上是否已经安装了 ngrok，请在终端中键入`ngrok help`。如果一个命令列表出现在终端中，你就可以开始了，如果你遇到一个错误`ngrok: command not found`，你需要在这里下载并安装 ngrok [。](https://ngrok.com/download)

一旦你安装了 ngrok，打开另一个终端，输入`ngrok http 3000`。您应该在您的终端中看到类似这样的内容:

[![screenshot](img/e4717a735ba5143a34c8ac63a2f20938.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZzlIFJk---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/644/0%2ALcg6uqsGS5liCQJj.png)

这意味着 ngrok 在您的本地主机上打开了一个端口 3000 的窗口，并通过“转发”将其显示的 URL 暴露给互联网(这将与上面截图中的不同)。

现在回到 Twilio 仪表盘，点击电话号码部分的“管理号码”,然后点击您将连接到我们应用程序的电话号码。

在“消息”部分，在“有消息进来”旁边，选择“Webhook”。在 Webhook 旁边的字段中，输入 ngrok 给你的 url，后跟`/twilio/sms`(这样看起来就像`https://ngrokurl.ngrok.io/twilio/sms`用你终端中的 URL 替换 ngrokurl)，选择“HTTP POST ”,然后点击“Save”。

[![screenshot](img/4c6e03e361d5b15258357bbdd2d0aadb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yd31dFQV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AhvqDqYZZrXbtWDsC.png)

现在，Twilio 会将任何传入的消息转发到我们指定的 ngrok 路由，该路由又会将其转发到我们位于端口 3000 的本地主机。让我们配置我们的应用程序来响应。

#### TwiML-dee TwiML-dum

Twilio API 使用 TwiML(代表 Twilio 标记语言)进行通信。类似于 XML 的标记方案)使用类似于 [< Message >](https://www.twilio.com/docs/sms/twiml/message) 的动词来发送文本消息，使用 [< Say >](https://www.twilio.com/docs/voice/twiml/say) 来发送语音。幸运的是，我们安装的 Twilio gem 将使用 Twilio::TwiML 类为我们创建 TwiML。让我们用它来创建应用程序的响应。

在`app/controllers/twilio_controller.rb`中，让我们把下面的代码放到`TwilioController`中:

```
class TwilioController < ApplicationController

    skip_before_action :verify_authenticity_token

    def sms

        body = helpers.parse_sms(params)

        response = Twilio::TwiML::MessagingResponse.new do |r|

            r.message body: body

        end

        render xml: response.to_s

    end

end 
```

它的作用是为 TwiML response 对象设置一个变量 Response，该对象的正文中包含文本“Hello World”。然后，我们将响应对象呈现为 XML。结果是如下所示的 XML 响应:

```
<?xml version=”1.0” encoding=”UTF-8”?>

<Response>

    <Message>Hello World</Message>

</Response> 
```

<figure>[![screenshot](img/f1528ad05fc1a67149fa54b0db80791c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iaEUhLRg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ax9uEQxUZkbSIv7zs.png) 

<figcaption>很确定，那里……</figcaption>

</figure>

我们来测试一下。在你的控制台中键入`rails server`(如果你的 rails 服务器还在运行，关闭它，然后重启，这样我们最新的更改就会生效)，然后发送短信到你的 Twilio 号码。您应该会收到一条回复为“Hello World”的短信。整洁不是吗？

(坦白:第一次看到我的手机亮起来，显示我用代码发送的一条短信，这是提醒我为什么一开始就爱上编码的那些时刻之一！)

### 都是关于猫的！

当然，尽管这很酷，但我们正在构建一个 *Cat Facts* 应用程序，而不是一个 *Hello World* 应用程序，所以让我们更改 TwiML 对象以返回更动态的内容。

让我们将我们的响应对象修改如下:

```
response = Twilio::TwiML::MessagingResponse.new do |r|

    r.message body: CatFact.last.fact

end 
```

如果您现在向您的 Twilio 号码发送短信，您应该会收到回复，其中包含您添加到应用程序中的最新卡特彼勒信息。

我们现在有一个应用程序，用户可以随时发送短信，并收到最新的卡特彼勒信息。多酷啊。！

### 订阅！

但是我们的用户想要更多；他们不想一直给我们发短信，希望我们添加新的事实，他们希望能够订阅，以便在新的卡特彼勒事实出现时立即收到通知！

要做到这一点，我们需要为我们的应用程序添加一些逻辑，以便它可以解析传入的文本消息并做出相应的响应。

我们可以把所有的逻辑都放在我们的`sms`控制器动作中，但是那会导致一个非常庞大的控制器。相反，我们将把它放在一个助手方法中。

但是在我们到达那里之前，我们需要有一种在我们的数据库中存储我们的订户的方法。

在您的终端键入:`rails generate model subscriber phone_number`。这将给我们一个带有 phone_number 属性的`Subscriber`模型。在你的终端上运行`rails db:migrate`，把那个表添加到我们的数据库中。

现在，我们可以构建一个助手方法来帮助我们解析用户的文本消息，以便他们可以订阅我们的应用程序。

让我们想想我们希望我们的用户能够做什么。

用户应该能够:

*   发送消息**订阅**订阅我们的应用
*   如果他们想获得最新的卡特彼勒信息，请发送 **FACT**
*   如果他们想停止接收通知，发送**取消订阅**

(谁会想停止获取卡特彼勒事实呢？我知道！也就是说，请不要做一个用户不能轻易退订的订阅型 app)

#### 解析消息

为此，我们将构建一个方法来分析传入的文本消息，查看它说了什么，并返回适当的响应。

转到`app/helpers/twilio_helper.rb`，将以下方法放入`TwilioHelper`模块:

```
def parse_sms(sms)

    body = sms[:Body]&.strip&.upcase

    from = sms[:From]

    case body

    when "SUBSCRIBE"

        subscriber = Subscriber.create(phone_number: from)

        return "The number #{from} has been subscribed to receive cat facts. Text UNSUBSCRIBE at any time to unsubscribe."

    when "UNSUBSCRIBE"

        subscriber = Subscriber.find_by(phone_number: from)

        if subscriber

            subscriber.destroy

            return "The number #{from} has been unsubscribed. Text SUBSCRIBE at any time to resubscribe."

        else

            return "Sorry, I did not find a subscriber with the number #{from}."

        end

    when "FACT"

        return CatFact.last.fact

    else

        return "Sorry I didn’t get that. the available commands are SUBSCRIBE, UNSUBSCRIBE, and FACT."

    end

end 
```

它所做的是定义一个`parse_sms`方法，该方法接受一个 SMS 作为参数(实际上只是随 GET 请求一起发送的 params hash twilio)。然后，该方法将 SMS 的正文传递给 switch 语句来检查它。如果短信正文说**订阅**，它将订阅来电号码，如果它说**取消订阅**，它将取消订阅该号码，如果它说**事实**，它将返回最新的 cat 事实。

在每种情况下，该方法都返回一条有用的消息。如果输入的文本与其中任何一个都不匹配，将返回一条有用的消息，其中包含可用的命令。

现在让我们更新我们的短信控制器如下:

```
def sms

    body = helpers.parse_sms(params)

    response = Twilio::TwiML::MessagingResponse.new do |r|

        r.message body: body

    end

    render xml: response.to_s

end 
```

我们所做的是获取传入的参数，并将它们传递给我们之前定义的`parse_sms`方法。

然后我们获取从`parse_sms`返回的字符串，并使用响应作为主体创建一个 TwiML 响应对象。然后，我们将 TwiML 对象转换成 XML，并将其发送回 Twilio。

### 但是这管用吗？

让我们来测试一下。如果你关闭了你的`rails server`,请再次启动它，然后给**发短信，订阅**你的 Twilio 号码。您应该很快会收到回复，告知您的号码已被订购。如果你现在去你的 rails 控制台运行`Subscriber.last.phone_number`，你的电话号码应该会出现。

### 快好了！

我们现在剩下的唯一一件事就是设置我们的应用程序，每当我们添加新的卡特彼勒事实时，它都会向我们的订户发送通知。

让我们转到`app/models/cat_fact.rb`，在我们的`CatFact`类中添加下面的方法:

```
def notify_subscribers

    client = Twilio::REST::Client.new

    Subscriber.find_each do |subscriber|

        client.messages.create(

            from: "YOUR_TWILIO_PHONE_NUMBER",

            to: subscriber.phone_number,

            body: self.fact

        )

    end

end 
```

该方法遍历我们的所有订户，并使用 Twilio REST API 向他们每个人发送一条以给定的 cat 事实为正文的文本消息(不要忘记使用格式`“+19876543210”`用您的实际 Twilio 号码替换`YOUR_TWILIO_PHONE_NUMBER`)。

我们现在要做的就是告诉我们的`CatFact`类在每次创建新的`CatFact`时调用这个方法。在您的`CatFact`类的顶部附近添加以下代码:

```
after_create :notify_subscribers 
```

现在，每次创建`CatFact`的实例时，都会调用`notify_subscribers`方法，并且我们所有的订阅者都会得到通知。

### 把所有的东西放在一起

我们现在准备测试我们的应用程序，看看它是否如预期的那样工作。

*   启动 rails 服务器(如果它还没有运行的话)
*   在浏览器中导航至[http://localhost:3000/cat _ facts](http://localhost:3000/cat_facts)
*   用新的卡特彼勒事实填写表格，然后点击“创建”
*   你(以及任何订阅了你的应用程序的人)应该会收到一条短信，告知你的新事实！

### 结论

这真的很酷，请随意使用它，看看还可以添加什么功能(例如，在我的备用侧停车机器人中，用户也可以打电话获得最新状态)。

Twilio 文档非常友好且易于使用，他们甚至有一个名为 [TwilioQuest](https://www.twilio.com/quest) 的游戏来帮助你开始！

如果你想看我们刚刚构建的应用程序的代码，你可以在 [GitHub](https://github.com/achasveachas/twilio_cat_facts) 上查看回购。

最后提醒一下，如果你住在纽约，请随时关注 [@AlterSideBot](https://twitter.com/AlterSideBot) 或发送短信**订阅**347–404–5618，以便在备用路边停车规则暂停时获得短信通知。

* * *

*如果你喜欢，你可以在他的博客 [Rabbi On Rails](https://blog.yechiel.me) 上阅读更多关于 Yechiel Kalmenson 的文章，或者在 Twitter 上关注他 [@yechielk](https://twitter.com/yechielk)*