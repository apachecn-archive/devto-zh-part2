# 使用 Twilio 通过 Python、Flask 和 Ngrok 发送 SMS 文本

> 原文：<https://dev.to/jovanhernandex/using-twilio-to-send-sms-texts-via-python-flask-and-ngrok-4i39>

[![](../Images/e8295323553e56fd79fc3a15b8fe713d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OeavfaS8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AaAMpcafIJX4xLKSSp1m0bg.png)

Python 在很多方面都很棒。特别是，有一件事是编写代码来开发和部署简单的 web 应用程序，以实现您业务中可能需要的特定功能或实用程序。在本教程中，我将介绍几种如何使用 Communications REST API 、 [Python 的 Flask 微框架](http://flask.pocoo.org)和 [ngrok](https://ngrok.com) 集成 [Twilio 的可编程 SMS 系统，后者是一种云服务，通过安全隧道将 NAT 和防火墙后面的本地服务器暴露给公共互联网。](https://www.twilio.com/docs/api?filter-product=sms)

### 出站短信—如何工作？

[![](../Images/240edfcffc7e95f64b7c00a7f832a456.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dsi8BwwI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AU7-ZBE0RJrCOnd_S0DS6hg.png)

使用 Twilio 的 REST API，您可以从您的 Twilio 电话号码向世界各地的手机发送短信。

您的应用程序向 Twilio 发出 post 请求。一旦收到，Twilio 就向 Python 脚本中声明的一个号码或声明的任何变量发送一条文本消息。就这么简单。

### 创建 Twilio 账户&购买号码

在开始之前，您需要在 Twilio 上开一个帐户，并购买一个发送消息的号码。这是一个相当简单的过程，你不需要为这个教程花费任何东西，因为他们会给你免费的积分来购买和测试他们的服务。如果你计划将 Twilio 整合到你的业务中，你将来会产生一些成本，所以浏览一下他们的定价模型是个好主意。

前往[Twilio.com](https://www.twilio.com)注册一个免费账户。注册后，你可以用他们给新成员的点数购买一个号码。为了简洁起见，[我将把你链接到他们关于如何购买号码的支持页面。](https://support.twilio.com/hc/en-us/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)

<figure>[![](../Images/db9da25c292ccd41544d4b70f8bddee8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b1IE572t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ad5J_f-4R0Y9Gt43lZTrUUA.png) 

<figcaption>确保您购买的号码可以发送短信。</figcaption>

</figure>

### 设置您的环境并安装软件包

在我的上一篇文章中，我讲述了如何[为 Python 开发、Flask 应用和 VirtualEnv](https://dev.to/jovansn0w/configuring-ubuntu-18-04-for-a-python-dev-environment-with-flask-gunicron-and-virtualenv-phc-temp-slug-3414608) 配置 Ubuntu。您可能想先检查一下，但是如果您已经检查过了，让我们从安装 twiliopackage 开始。*(注意:对于本教程，您应该使用 Python3。)*

```
pip install twilio 
```

安装了 Twilio 之后，我们现在可以通过一个简单的 Python 脚本，从我们购买的号码向目标号码发送出站消息。创建并打开一个名为 send _ SMS . py 的新文件，然后键入或粘贴此代码示例。确保将占位符值替换为您自己的信息*(即您的* *account_sid、* *auth_token、* *body、* *from 和* *tofields)。您可以在您的* [*Twilio 控制台中找到您的帐户 SID 和身份验证令牌。*](https://www.twilio.com/console) *)*

```
# Download the helper library from [https://www.twilio.com/docs/python/install](https://www.twilio.com/docs/python/install)

from twilio.rest import Client

# Your Account Sid and Auth Token from twilio.com/console
account\_sid = ‘AC00000000000000000000000000000’
auth\_token = ‘your\_auth\_token’
client = Client(account\_sid, auth\_token)

message = client.messages.create(
 body=’This is a test message!’,
 from\_=’[+][1][5555555555]',
 to=’[+][1][6666666666]'
 )
print(message.sid) 
```

<figure>[![](../Images/bbbbccdcffc323a6bb6f469bf3e0c508.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kxSLL-Cv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ap9dM36e1Z4Q3ZZT4iArK9g.png) 

<figcaption>用自己的信息替换占位符值。</figcaption>

</figure>

完成后，保存并使用下面的命令运行脚本:

```
python send\_sms.py 
```

就是这样！你的 Twilio 电话号码很快就会有一条信息打到你的手机上。如果您是试用帐户，您将会看到邮件的前缀。

<figure>[![](../Images/0910efb2c3b56cc37f20a25c3e478f5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MiQjyY_f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1002/1%2AMDxpF34OJRMdg8NHWHlnyg.png) 

<figcaption>无视我的高飞测试消息:P</figcaption>

</figure>

### 接收和回复收到的短信

为了接收和回复收到的短信，我们需要安装 Flask，设置 virtualenv 沙箱，并配置 ngork，以便 Twilio 可以与我们的应用程序对话。我们开始吧。

#### 安装 Pip 和 Virtualenv

如果你对 Python 开发很熟悉，或者看过我上一篇关于设置开发环境的文章，这应该很熟悉。幸运的是，如果您使用的是 Python 3.4+，pip 应该会自动安装。如果不是，谷歌就是你的朋友。

不安装 virtualenv，只需运行以下命令:

```
pip install virtualenv 
```

安装完成后，导航到保存该项目的目录，并创建一个虚拟环境。

```
cd ~/path\_to\_your\_project
virtualenv — no-site-packages .

###ACTIVATE THE VIRTUALENV SANDBOX
source bin/activate 
```

激活后，您应该会在终端中看到项目沙箱的名称。如果你感到困惑或遇到了问题，请在这里看我以前的帖子。

#### 在您的虚拟机中安装依赖项

Virtualenv 非常适合测试应用程序和安装每个项目特有的包和依赖项。对于这个 Twilio 应用程序，我们需要两个包。首先创建一个名为 requirements.txtand 的新文件，并添加以下几行:

```
###YOUR VERSIONS MAY VARY WHEN DEPENDING WHEN YOU GO THROUGH THIS TUTORIAL

Flask\>=0.12
twilio~=6.16.0 
```

然后通过运行以下命令安装这些软件包:

```
bin/pip install -r requirements.txt 
```

如果一切顺利，我们可以通过创建一个测试文件并在本地运行它来测试一切。确保您的 virtualenv 已激活，导航到您的项目文件夹并创建一个名为 run.py 的新文件。粘贴或编写以下代码:

```
from flask import Flask
app = Flask(\_\_name\_\_)

[@app](http://twitter.com/app).route(“/sms”)
def hello():
 return “This is your Twilio App Working!”

if \_\_name\_\_ == “\_\_main\_\_”:
 app.run(debug=True) 
```

<figure>[![](../Images/08e5b490b6407cb9f6853fb4c5fb0958.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gt8bekp5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-xvjRQdrbWWyzfaI1dtsKA.png) 

<figcaption>烧瓶中 Twilio app 的测试代码。</figcaption>

</figure>

现在我们通过运行 python run.py.

来运行脚本你应该看到:*运行在[http://127 . 0 . 0 . 1:5000/](http://127.0.0.1:5000/)

Naviage 到 localhost:5000/sms。您应该会在浏览器中看到以下文本:

<figure>[![](../Images/ef175c09ce7387eeb1c60873133047fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PPBA2t8a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACqFczmNCN9n3MpCsumO1OQ.png) 

<figcaption>你应该看看你做得对不对。</figcaption>

</figure>

### 允许 Twilio 使用 ngrok 与您的 Flask 应用程序对话

当您在开发环境中使用 Flask 应用程序时，您的应用程序只能由计算机上的其他程序访问。这就是为什么您的应用程序只能在 localhostor 127.0.0.1 上使用。为了解决这个问题，我们可以在 Heroku、AWS 或其他云服务上部署我们的应用程序，但对于这个特定的教程，我们将使用 ngrok。

根据他们自己的网站，ngrok *允许你将运行在你本地机器上的网络服务器暴露给互联网。只要告诉 ngrok 你的 web 服务器在监听哪个端口就行了。”*一旦启动，Ngrok 会在 ngrok.io 域上提供一个惟一的 URL，将传入的请求转发到您的本地开发环境。如果你从未使用过 ngrok，去他们的[下载页面](https://ngrok.com/download)下载你的操作系统的二进制文件。

一旦下载并解压缩了包，我们就可以运行我们之前制作的测试应用程序:python run.py。然后在一个新的终端应用程序中，我们将使用 ngrok 初始化一个会话，命令如下:

```
./ngrok http 5000 
```

您应该会在下面屏幕截图的左上角看到类似于终端的输出:

<figure>[![](../Images/80b19abc239196b12937662a3a9297dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W1ct-y0---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkCz1zOyP7XsspTMqTyNLJA.png) 

<figcaption>Ngrok 让你公开访问本地托管的应用。</figcaption>

</figure>

复制您唯一的公共地址，将其放入浏览器，并使用 *"/sms/"* 进行跟踪。例如，上面的地址应该是“*[【http://a0f8f155.ngrok.io/sms】](http://a0f8f155.ngrok.io/sms)*”。

### 回复 Twilio 发来的短信

当有人向您的 Twilio 电话号码发送短信时，Twilio 会向您的服务器发出 HTTP 请求，询问下一步该做什么。一旦你收到请求，你可以告诉 Twilio 回复一条短信，开始打电话，在你的数据库中存储信息的细节，或者触发一个完全不同的动作。

对于这个特定的教程，我们将让 Twilio 用一个确认回执来回复用户的响应。使用文本编辑器再次打开 run . py 并编辑代码以反映以下内容:

```
# /usr/bin/env python
# Download the twilio-python library from twilio.com/docs/libraries/python
from flask import Flask, request
from twilio.twiml.messaging\_response import MessagingResponse

app = Flask(\_\_name\_\_)

[@app](http://twitter.com/app).route("/sms", methods=['GET', 'POST'])
def sms\_ahoy\_reply():
 """Respond to incoming messages with a receipt SMS."""
 # Start our response
 resp = MessagingResponse()

# Add a message
 resp.message("Thank you for your response! We are confirming your message.")

return str(resp)

if \_\_name\_\_ == "\_\_main\_\_":
 app.run(debug=True) 
```

请随意编辑上面的代码，尤其是# Add a message 下面的那一行。我边看矩阵边摆弄我的代码，所以我的消息在下面的例子中会显得很愚蠢。

保存文件并再次运行 python run . py 命令。确保 ngrok 仍在运行，并复制他们给你的自定义公共 URL。为了让 Twilio 知道要抓取哪个 URL，我们需要配置我们的 Twilio 电话号码，以便每当有新消息进来时调用您的 webhook URL，这就是 ngrok 为我们做的。

登录 Twilio.com，进入[控制台的数字页面](https://www.twilio.com/console/phone-numbers/incoming)，点击你支持短信的电话号码，找到信息区。默认的“配置”是你需要的:“ [Webhooks](https://www.twilio.com/docs/glossary/what-is-a-webhook) / [TwiML](https://www.twilio.com/docs/glossary/what-is-twilio-markup-language-twiml) ”。

在“有消息进来”部分，选择“Webhook”并粘贴您想要使用的 URL。

<figure>[![](../Images/f90525132f504d2a83ffe128073f7c5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mVWMQAUd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARHtUjvH5J8i3CWs7y3wkXA.png) 

<figcaption>将您的自定义 ngrok URL 粘贴到 Twilio 控制台号码的页面中。</figcaption>

</figure>

现在，让我们通过执行您的两个 python 脚本来测试您的整个短信应用程序。

```
###RUN THIS TO SEND THE INITIAL TEXT
python send\_sms.py

###RUN THIS TO MAKE SURE TWILIO IS LISTENING FOR THE RESPONSE
python run.py 
```

<figure>[![](../Images/41bb82ba1bb52ddf13b00c750e28fca5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zBFvnunz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AiXaD_Pi0DV30KkW5lERE3A.png) 

<figcaption>万物一起运转。您可以通过访问以下网址来确认您的响应脚本是否公开。</figcaption>

</figure>

您应该收到您的初始文本，如果您回复它，您应该得到您在第二个 python 脚本中编码的响应文本。

<figure>[![](../Images/52f3cce5c78357f99f50a51e0242219a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ar26FXKR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1002/1%2AyhWkbxADa1LCXoZLSK9tjA.png) 

<figcaption>再次，忽略我的高飞矩阵引用:P</figcaption>

</figure>

差不多就是这样！利用本教程中的知识，您可以构建一个您已经很可能与之交互过的成熟的 web 应用程序。医生和牙医等许多职业使用这种服务来确认预约，甚至使用优步等拼车应用来确认用户准备好被接走。

还有许多其他 Twilio 教程指导您完成类似的过程。将其中一些拼凑在一起，构建您自己的定制应用程序！

* * *