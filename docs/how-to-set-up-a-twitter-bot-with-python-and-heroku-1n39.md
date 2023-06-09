# 如何用 Python 和 Heroku 建立一个 Twitter 机器人

> 原文：<https://dev.to/emcain/how-to-set-up-a-twitter-bot-with-python-and-heroku-1n39>

我刚刚设置了一个推特机器人来生成药品广告，并每天在推特上发布几次。(源代码[此处](https://github.com/emcain/drug_names))。为了做到这一点，我最终将几个不同教程中的概念拼凑在一起，没有一个教程列出了整个过程，所以我在这里写下来了。

[![image description: xkcd comic 1988\. Transcript can be found at https://www.explainxkcd.com/wiki/index.php/1988:_Containers](img/e2736a9576f1f2c13ac7063c7b3487f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XgezSJrb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/containers.png)

这些步骤可以总结为:

1.  编写一个函数来为你的机器人生成文本字符串
2.  创建一个**推特账号**，并在其下设置一个**应用**
    *   使用 Google Voice 设置电话号码
    *   为您的帐户分配该电话号码，允许您创建应用程序
3.  编写一个脚本，让**连接到您的 Twitter 应用程序**，并向它发送一个 API 调用，让**用 tweet 发送您的函数在步骤 1 中的输出**
4.  设置一个 **Heroku app** 作为**工作者**运行步骤 3 中的功能
5.  在你的 Heroku 应用中设置一个基本的**服务器**,这样 Heroku 就不会崩溃并抛出错误
6.  在 Heroku 中设置**环境变量**作为你的 Twitter 应用的凭证。

第 4 步到第 6 步也可以用来设置在 Heroku 上运行的任何 Python 脚本。

我使用谷歌的一些功能来为 Twitter 帐户生成一个独特的电子邮件和电话号码，但你可以用你喜欢的任何服务来创建它们。

本文假设对以下概念有一些基本的背景知识:Python、REST APIs、Git、环境变量。

因为它使用 Heroku，所以应用程序必须保存在 Git 存储库中。

另外，我使用了一个虚拟环境，并通过运行
将需求保存在一个文件中

```
$ pip freeze > requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

当您设置 Heroku 时，这就变得很重要，Heroku 使用`requirements.txt`来安装您需要的 Python 包。

## 1。生成字符串

不久前，我开发了这个功能。对于制药机器人，我使用马尔可夫链来生成药品名称，然后将它们插入到随机生成的模板中，得到这样的广告:

> 担心你可能有小骨抽搐？Sudate 可以帮忙！
> 
> 你被不安分的手肘困扰吗？今天就向你的医生咨询关于 Estron 的问题！
> 
> 你不必独自面对浮夸的脾脏。今天就向你的医生询问 Ryzema Prenztriumetine！

创造这些的函数叫做 [`get_ad`](https://github.com/EMCain/drug_names/blob/master/generate_advertisement.py) 。

在您自己的代码中，用一个生成您喜欢的任何类型文本的函数来替换`get_ad`。

## 2。创建一个 Twitter 应用

你可能想给机器人一个自己的账户，而不是从现有的账户发微博，所以设置一个。

Twitter 帐户将需要一个独特的电子邮件。如果你想使用一个已经有自己的 Twitter 账户的电子邮件地址，你可以利用 Gmail 的功能来注册一个进入同一个收件箱的[“备用”电子邮件。](https://fieldguide.gizmodo.com/how-to-use-the-infinite-number-of-email-addresses-gmail-1609458192)

你还需要在应用程序中设置一个电话号码；同样，您可以使用谷歌创建一个转到现有号码的备用联系人，这次使用的是[谷歌语音](https://www.wikihow.com/Get-a-Google-Voice-Phone-Number)。
接下来，进入你的 Twitter 应用仪表板[创建一个新的应用](https://apps.twitter.com/app/new)(以机器人账户登录)。

[![a screenshot of the Twitter application creation page](img/567c10b65e8a90d96a0bd7a8e0656d21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eVCD2iYp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7tr5lr7p4gpr27qugk1h.png)

对于应用程序的网站，我将 GitHub 的 URL 放在这个项目的 repo 所在的位置。

创建应用程序后，请确保它允许读写访问。

## 3。连接到 Twitter 应用程序并发送推文

我用这篇文章[如何用 Python 和 tweepy](https://dototot.com/how-to-write-a-twitter-bot-with-python-and-tweepy/) 编写一个 Twitter Bot 作为这一部分的粗略指南。查看关于设置你的 Twitter 应用程序和通过 API 连接到它的指南。请注意，您需要为您的 Twitter 应用程序添加一个“访问密钥”。

他们让机器人从文本文件中读取行；相反，我有我的电话。这样做的脚本叫做 [bot.py](https://github.com/EMCain/drug_names/blob/master/bot.py) 。下面是生成和发送推文的部分:

```
from generate_advertisement import get_ad

INTERVAL = 60 * 60 * 6  # tweet every 6 hours
# INTERVAL = 15  # every 15 seconds, for testing 
auth = tweepy.OAuthHandler(CONSUMER_KEY, CONSUMER_SECRET)
auth.set_access_token(ACCESS_KEY, ACCESS_SECRET)
api = tweepy.API(auth)

while True:
    print("about to get ad...")
    ad = get_ad()
    api.update_status(ad)
    time.sleep(INTERVAL) 
```

Enter fullscreen mode Exit fullscreen mode

然后，我在本地终端上运行了`bot.py`。

成功！

[![screenshot of initial tweets sent by bot](img/6c6cce182e3d60ace1ac2709107feeda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0YNr5y9i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k2bh0dvw0fielp82ww4t.png)

它能够很好地发送推文，但我不想让我的笔记本电脑和我的终端永远开着，所以我需要让它在某个地方自己的服务器上运行。我的下一个任务是给 Heroku 装上机器人。

### 重要提示:不要提交你的密钥

通过 API 连接到 Twitter(包括通过像`tweepy`这样的包装库)需要你使用密钥来识别你的应用程序并验证你的用户身份。确保不要在 Git 存储库中提交这些密钥——这就像发布密码一样。即使您后来从代码中删除了这些键，它们也会出现在您的提交历史中。

最初，我将密钥存储在一个名为`credentials.py`的文件中，并将其添加到我的`.gitignore`中。看起来是这样的:

```
CONSUMER_KEY = 'consumer key'
CONSUMER_SECRET = 'consumer secret'
ACCESS_KEY = 'access key'
ACCESS_SECRET = 'access secret' 
```

Enter fullscreen mode Exit fullscreen mode

然后，我将密钥导入到`bot.py`中。当我把这个应用程序放在 Heroku 上时，它并没有成功，但在我的本地机器上测试它还是不错的。

## 4。建立 Heroku

如果你还没有一个 Heroku 帐户，请注册一个。Heroku 允许你用 Git 推送和部署代码，你可以免费创建最多 5 个 app。

一旦你安装了 Heroku 命令行界面，用 Heroku 初始化你的库。如果你让它为你的应用程序生成一个随机的 URL，这是可以的，因为没有人会直接看到它。

```
$ heroku create 
```

Enter fullscreen mode Exit fullscreen mode

这将建立一个 Heroku 应用程序，并为您的 Git 存储库提供一个名为`heroku`的遥控器。遥控器的`master`分支上的代码将由 Heroku 运行。

你需要设置一个`Procfile`来告诉 Heroku 一旦得到你的代码该做什么。把这个放进你的`Procfile` :

```
worker: python bot.py 
```

Enter fullscreen mode Exit fullscreen mode

这意味着 Heroku 将执行与您在本地终端上运行`python bot.py`相同的操作。

然后提交你的代码，推给 Heroku。

从本地`master`分支进行部署:

```
$ git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

从不同的分支部署:

```
$ git push heroku my_branch_name:master 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以登录 Heroku 仪表板，检查其活动和日志，看看发生了什么。

## 5。基本 Web 服务器

原来 Heroku 不喜欢仅仅用 workers 运行脚本，因为它希望看到一个 web 服务器。

您可以通过创建一个简单的 web 服务器来解决这个问题。我用[这个 StackOverflow 答案](https://stackoverflow.com/a/39170561)作为指导。

> 代码(server.py):

```
from os import environ
from flask import Flask

app = Flask(__name__)
app.run(environ.get('PORT')) 
```

Enter fullscreen mode Exit fullscreen mode

> 然后，在您的 Procfile 中，只需说:web: python server.py。

我尝试了这一点(并再次提交和推给 Heroku)。然后我看到了下面的错误:

```
Error R10 (Boot timeout) -> Web process failed to bind to $PORT within 60 seconds of launch python flask 
```

Enter fullscreen mode Exit fullscreen mode

我在运行应用程序时通过设置主机修复了这个问题:

```
app.run(host= '0.0.0.0', port=environ.get('PORT')) 
```

Enter fullscreen mode Exit fullscreen mode

## 6。设置环境变量

还记得我说过不要将您的 Twitter 凭证提交到您的 Git 存储库吗？当您部署到 Heroku 时，这就成了一个问题，因为它使用您的 Git repo 进行部署，所以从一个被忽略的文件中导入常量是行不通的。

您可以[在 Heroku 仪表板](https://devcenter.heroku.com/articles/config-vars#using-the-heroku-dashboard)中设置环境变量，然后使用`os.environ`在 Python 中获取它们。在进行 API 调用之前，将其添加到`bot.py`:

```
from os import environ
CONSUMER_KEY = environ['CONSUMER_KEY']
CONSUMER_SECRET = environ['CONSUMER_SECRET']
ACCESS_KEY = environ['ACCESS_KEY']
ACCESS_SECRET = environ['ACCESS_SECRET'] 
```

Enter fullscreen mode Exit fullscreen mode

然后你的应用程序将能够连接到 Twitter API，而不会泄露你的秘密。

## 接下来是什么？

现在你知道如何创建一个 Twitter 机器人并在 Heroku 上运行它了！

你可以修改这个项目来发布任何你喜欢的东西。[本文](http://www.onthelambda.com/2014/02/20/how-to-fake-a-sophisticated-knowledge-of-wine-with-markov-chains/)讲解如何使用 Python 生成带马尔可夫链的文本。你可以使用[如何用 Python 和 tweepy 编写 Twitter 机器人](https://dototot.com/how-to-write-a-twitter-bot-with-python-and-tweepy/)中概述的方法创建一个文本文件，每行包含一个你想发布的句子。你可以在[古腾堡项目](https://www.gutenberg.org/)发布来自公共领域作品的微博。或者做任何你想做的事！

注意:本文的早期版本发表在 [emcain.github.io](https://emcain.github.io) 。