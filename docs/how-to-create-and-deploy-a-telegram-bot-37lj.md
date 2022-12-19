# 如何创建和部署电报机器人指南

> 原文：<https://dev.to/django_stars/how-to-create-and-deploy-a-telegram-bot-37lj>

### 这是介绍，你可以[跳过](#skip)

在本教程中，我们将使用 Python 构建一个非常简单的机器人，并将其部署在 Heroku 服务器上。

在接下来的段落中，我将提供一些关于机器人实际上是什么以及为什么创建它们会让你感兴趣的见解。所以如果你已经知道了，就跳过这一部分。

从表面上看，Telegram 只是另一款信息应用。该应用程序宣传自己是安全、快速、无广告的。然而，有一个功能肯定能让它在同类应用中脱颖而出。那是机器人！

[![](../Images/87032dd074be91de578b88cc92a5786a.png)T2】](/blog/content/images/2017/02/image00.jpg)

对于那些仍然不熟悉这个概念的人，把它想象成某种脚本化的自动回复“实时聊天”代表，抱歉，但这一次你错了。这些家伙是你可以在任何聊天中使用的惊人的小帮手。你可以把 bot 想象成一个自动化的用户账户，可以为你做一些小把戏。例如，您想要在群组对话中共享 YouTube 链接，但是您还没有链接。

**不带机器人:**

*   你在网络浏览器中打开 YouTube。
*   搜索您想要分享的视频
*   选择共享方式...(并希望你的应用被列入 YouTube 分享列表)
*   跳过上面的步骤，做一个老派的复制粘贴
*   回到你的信息应用，最后分享链接
*   Woohoo

当然，我们大多数人已经习惯了上面的算法，它对我们来说是有效的。然而...

**与机器人:**

*   您正在通过您的消息应用程序进行交流。
*   输入 **@vid** ，然后输入您想要查找和分享的视频
*   按下或轻按 return

上面的方法更好，更方便用户，更省时。这只是机器人能力的一个例子。

Telegram 做得很好，允许用户创建自己的机器人。在回答为什么会有趣这个问题时，我可以说这是理解 API 的最简单的方法。

### 创建你的第一个机器人怎么样？

重要的事情先来。你需要在电报上登记(很明显)。我推荐使用 [Telegram web 客户端](https://web.telegram.org/)来测试基本概念。

打开 Telegram app，搜索 **[@botfather](https://dev.to/botfather)** ，开始聊天。发送 **/newbot** 命令并按照指示操作。完成初始步骤后，您将获得—

*   你的令牌
*   电报 API URL—**[https://api.telegram.org/bot](https://api.telegram.org/bot)**
*   [文档链接](https://core.telegram.org/bots/api)

威尔斯事实上就是这样。目前，机器人是 100%被动的。

您需要初始化与您的机器人的对话。打开搜索，输入你的机器人的名字。点击**/开始**按钮开始对话。键入类似“你好”的内容。这条消息很重要，因为它是你的机器人将收到的第一个更新。

如果这是您第一次体验构建 API，您可以使用 web 浏览器轻松了解这个想法。在浏览器中打开一个新标签，使用电报 api URL -

**[https://api.telegram.org/bot&# 60；令牌&# 62；/get updates](https://api.telegram.org/bot<token>/getUpdates)T3】**

当您在 web 浏览器中打开这个 URL 时，您向 Telegram 服务器发出请求，它用 JSON 作出响应。响应类似于 Python 字典。您应该会看到类似这样的内容:

```
{"ok":true,"result":[{"update_id":523349956,
"message":{"message_id":51,"from":{"id":303262877,"first_name":"YourName"},"chat":{"id":303262877,"first_name":"YourName","type":"private"},"date":1486829360,"text":"Hello"}}]} 
```

Enter fullscreen mode Exit fullscreen mode

如果你打开 bots 文档并检查 **/sendMessage** 方法[部分](https://core.telegram.org/bots/api#sendmessage)，你会注意到这个方法需要两个额外的参数 **chat_id** 和 **text** 。在浏览器搜索栏中，您可以使用**链接参数。**为第一个， **&** 为所有的后件。发送的消息应该是这样的-

```
/sendMessage?chat_id=303262877&text=test 
```

Enter fullscreen mode Exit fullscreen mode

试着通过调用 **/getUpdates** 将 **chat_id** 替换为一个你得到的回复。我的号码是 303262877。文本参数由您决定。请求应该是这样的

```
https://api.telegram.org/bot<token>/sendMessage?chat_id=303262877&text=Hello 
```

Enter fullscreen mode Exit fullscreen mode

### 几乎编码部分

如果你用的是 Windows，没有安装 Python，你可以在这里获得它[。](https://www.python.org/downloads/windows/)

不管您使用的是 2.x 还是 3.x 版本，我都将使用 Python 3.x 作为示例。如果你使用的是 Linux/Mac，很可能你已经安装了这两个版本或者至少安装了 Python 2.x。

下一步是安装 pip。Python 2.7.9 及以上，Python 3.4 及以上已经包含了 pip。

在 macOS/Linux 上，你可能再次拥有它。您可以在终端中使用 **pip - version** 命令进行检查。

如果你因为某种原因没有安装 pip，你可以使用下面的命令
在基于 Debian 的 Linux 上安装 pip

```
$ sudo apt-get install python-pip. 
```

Enter fullscreen mode Exit fullscreen mode

棘手的部分是不同版本的 python 使用自己的 pip。

在 Mac OS 上，你可以尝试来自[这里](http://stackoverflow.com/questions/17271319/how-to-install-pip-on-macos-os-x)的指令

在 Windows 下载 get-pip.py，打开 cmd，转到保存文件的目录，运行以下命令—

```
$ python get-pip.py 
```

Enter fullscreen mode Exit fullscreen mode

这是最难的部分。

接下来，您需要使用 pip 安装请求包。使用下面的命令-

```
$ pip install requests 
```

Enter fullscreen mode Exit fullscreen mode

下一步是可选的，但它会有很大的帮助。安装 [PyCharm](https://www.jetbrains.com/pycharm/) ，它摇滚。

### 编码部分

如果 API 的想法很清楚，并且您已经有了所有必要的工具，那么让我们制作一个 Python 脚本，它将检查更新并返回所需的文本。

首先，我们的机器人应该检查更新。我们的消息可以被视为最近的更新。但是，getUpdates 将返回过去 24 小时的所有更新。让我们创建一个小脚本来获取最后一次更新。

```
import requests

url = "https://api.telegram.org/bot<token>/"

def get_updates_json(request):
    response = requests.get(request + 'getUpdates')
    return response.json()

def last_update(data):
    results = data['result']
    total_updates = len(results) - 1
    return results[total_updates] 
```

Enter fullscreen mode Exit fullscreen mode

更新字典由两个元素组成——“ok”和“results”。我们对“结果”部分感兴趣，这是我们的机器人在过去 24 小时内获得的所有更新的列表。

你可以在请求库[这里](http://docs.python-requests.org/en/master/)查看更多信息。最基本的想法是，每当你需要获取、更新或删除服务器上的信息时，你发送一个请求并得到一个响应。参数

下一步是添加 2 个功能。第一个将从 update 获取 chat_id，第二个将发送一条消息。

```
def get_chat_id(update):
    chat_id = update['message']['chat']['id']
    return chat_id

def send_mess(chat, text):
    params = {'chat_id': chat, 'text': text}
    response = requests.post(url + 'sendMessage', data=params)
    return response

chat_id = get_chat_id(last_update(get_updates_json(url)))

send_mess(chat_id, 'Your message goes here') 
```

Enter fullscreen mode Exit fullscreen mode

还记得用“？”链接参数吗还有“&”？您可以通过将 dict 作为第二个可选参数添加到 requests get/post 函数来完成同样的操作。

剧本准备好了。然而，它远非完美。一个主要的缺点是，每次想用 bot 发送消息时，都需要运行脚本。让我们修理它。为了让我们的机器人监听服务器以获取更新，我们需要启动一个主循环。在**导入请求**之后的新行上添加**从时间导入睡眠**。

```
def main():
    update_id = last_update(get_updates_json(url))['update_id']
    while True:
        if update_id == last_update(get_updates_json(url))['update_id']:
           send_mess(get_chat_id(last_update(get_updates_json(url))), 'test')
           update_id += 1
    sleep(1)       

if __name__ == '__main__':
    main() 
```

Enter fullscreen mode Exit fullscreen mode

虽然我们添加了 1 秒的“超时”，但是上面的例子应该只用于测试目的，因为它使用了一个短轮询。这对电报服务器没有好处，应该避免。有两种方法可以获得 bot api 的更新——长轮询或 webhook。但是，如果我们使用不带任何参数的 getUpdates 方法检查更新，将使用短轮询。

当我们开始在脚本中使用主循环时，我们需要切换到长轮询。

为了让我们的脚本使用长轮询，我们需要通过添加超时参数来修改第一个函数。

超时本身不会降低脚本检查更新的频率。只有在最近没有更新的情况下，超时才会起作用。如果你想表明某个更新已经被看到，你需要添加“偏移”参数。

```
def get_updates_json(request):
    params = {'timeout': 100, 'offset': None}
    response = requests.get(request + 'getUpdates', data=params)
    return response.json() 
```

Enter fullscreen mode Exit fullscreen mode

现在这个机器人应该可以正常工作了，但是让我们稍微修改一下整个代码。封装我们到目前为止用来创建一个类的所有函数是一个好主意。所以修改后的版本可能看起来像这样—

```
import requests
import datetime

class BotHandler:

    def __init__(self, token):
        self.token = token
        self.api_url = "https://api.telegram.org/bot{}/".format(token)

    def get_updates(self, offset=None, timeout=30):
        method = 'getUpdates'
        params = {'timeout': timeout, 'offset': offset}
        resp = requests.get(self.api_url + method, params)
        result_json = resp.json()['result']
        return result_json

    def send_message(self, chat_id, text):
        params = {'chat_id': chat_id, 'text': text}
        method = 'sendMessage'
        resp = requests.post(self.api_url + method, params)
        return resp

    def get_last_update(self):
        get_result = self.get_updates()

        if len(get_result) > 0:
            last_update = get_result[-1]
        else:
            last_update = get_result[len(get_result)]

        return last_update 
```

Enter fullscreen mode Exit fullscreen mode

现在，最后一步是声明变量，并教这个机器人一些礼仪。这个想法是制造一个机器人，它会每天向你问候一次。根据一天中的不同时间，回复会有所不同。如果您想尝试这个脚本，您需要在导入请求后的下一行添加**导入日期时间**，并将以下代码添加到您的脚本

```
greet_bot = BotHandler(token)
greetings = ('hello', 'hi', 'greetings', 'sup')
now = datetime.datetime.now()

def main():
    new_offset = None
    today = now.day
    hour = now.hour

    while True:
        greet_bot.get_updates(new_offset)

        last_update = greet_bot.get_last_update()

        last_update_id = last_update['update_id']
        last_chat_text = last_update['message']['text']
        last_chat_id = last_update['message']['chat']['id']
        last_chat_name = last_update['message']['chat']['first_name']

        if last_chat_text.lower() in greetings and today == now.day and 6 <= hour < 12:
            greet_bot.send_message(last_chat_id, 'Good Morning  {}'.format(last_chat_name))
            today += 1

        elif last_chat_text.lower() in greetings and today == now.day and 12 <= hour < 17:
            greet_bot.send_message(last_chat_id, 'Good Afternoon {}'.format(last_chat_name))
            today += 1

        elif last_chat_text.lower() in greetings and today == now.day and 17 <= hour < 23:
            greet_bot.send_message(last_chat_id, 'Good Evening  {}'.format(last_chat_name))
            today += 1

        new_offset = last_update_id + 1

if __name__ == '__main__':
    try:
        main()
    except KeyboardInterrupt:
        exit() 
```

Enter fullscreen mode Exit fullscreen mode

从这里有数以千计的方法来定制你的机器人。我建议尝试发送媒体方法或添加“自定义”按钮。

### 进入野外。部署

使您的 bot 成为真正的 bot 的最后一步是将其部署在服务器上。很可能你没有自己的服务器，也不想购买...你不需要这么做。目前，有很多云解决方案可以免费托管你的应用。我将向您展示如何将这个小脚本部署到 Heroku。

首先你需要一个 [GitHub](https://github.com/) 账号。去注册吧，如果你对任何类型的编程感兴趣，这是一个必不可少的步骤。除了 GitHub 账号，还需要安装 git。

在基于 Debian 的 Linux 上运行以下命令—

```
$ sudo apt-get install git-all 
```

Enter fullscreen mode Exit fullscreen mode

下载 Git 用于:

**[macOS](http://git-scm.com/download/mac)**

**[视窗](http://git-scm.com/download/win)**

报名 **Heroku** [这里](https://signup.heroku.com/)

使用下面的命令-
安装 **virtualenv**

```
$ pip install virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

现在你需要稍微整理一下你的文件。创建一个新文件夹，打开终端/CMD，进入新文件夹。

在新文件夹中初始化 virtualenv 输入

```
$ virtualenv my_env 
```

Enter fullscreen mode Exit fullscreen mode

名字并不重要，但是最好保持足够的描述性。前往 my_env 文件夹。

下一步—克隆您的 git 存储库。键入以下命令:

```
$ git clone https://github.com/yourprofilename/yourreponame 
```

Enter fullscreen mode Exit fullscreen mode

把你的脚本放在 git 克隆文件夹中。

返回 my_env 文件夹，使用以下命令启动 virtualenv

**视窗:**

```
$ scripts\activate.bat 
```

Enter fullscreen mode Exit fullscreen mode

**Linux/MAC OS:**T2】

```
$ source bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

如果您成功激活了 **virtualenv** ，您的控制台提示符应该以(my_env)开头。

下一步—转到您克隆的 repo，通过键入—
再次安装 Python 请求模块

```
$ pip install requests 
```

Enter fullscreen mode Exit fullscreen mode

下一步是为 Heroku 创建一个依赖列表。很简单，只要输入

```
$ pip freeze > requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

创建一个**过程文件**。在这个文件中，您将提供如何处理脚本的指令。名字应该完全是 windows 的 **Procfile** 和 **Procfile.windows** 。不应该的。txt 或者。py 或任何其他扩展名。文件的内容应该如下(用你的脚本的名字改变我的机器人)

```
web: python my_bot.py 
```

Enter fullscreen mode Exit fullscreen mode

添加 **__init__。文件夹中的 py** 文件。文件可以是空的，但它应该在那里。

键入以下一系列命令来提交和推送您所做的更改。

```
$ git init
$ git add .
$ git commit -m ‘short message that describe changes to commit’
$ git push -u https://github.com/yourusername/nameofrepo 
```

Enter fullscreen mode Exit fullscreen mode

下一步是在 Heroku 上的实际部署。出于某种原因，我无法使用他们的 web 浏览器仪表板来完成它。然而，使用 Heroku 命令行界面非常容易。如果你面临任何麻烦，我建议从本指南开始。

我将只提供几个步骤，应该足以部署一个应用程序。如果你在 Mac 或 Windows 上，你可以从[这一步](https://devcenter.heroku.com/articles/getting-started-with-python#set-up)下载 CLI。

如果你像我一样使用 Ubuntu，使用以下命令

```
$ sudo add-apt-repository "deb https://cliassets.heroku.com/branches/stable/apt ./"
$ curl -L https://cli-assets.heroku.com/apt/release.key |
$ sudo apt-key add -
$ sudo apt-get update
$ sudo apt-get install heroku 
```

Enter fullscreen mode Exit fullscreen mode

在我的家用电脑上，一切都很顺利，然而在我的第二台笔记本电脑上，我无法马上完成最后一步。如果您面临同样的问题，请检查终端的提示和缺少的依赖项。

遵循下一组命令-

```
$ heroku login
$ heroku create
$ git push heroku master
$ heroku ps:scale web=1
$ heroku open 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，你的应用程序应该可以在 Heroku 服务器上运行了。如果由于某种原因它不工作，使用下面的命令检查日志—

```
$ heroku logs --trail 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里找到错误代码

有了免费账户，你将面临某些限制。然而，这里你有一个功能齐全的机器人。

这篇文章是罗曼·加波诺夫写的。这篇关于 [telegram bot](https://djangostars.com/blog/how-to-create-and-deploy-a-telegram-bot/) 的文章最初发表在 Django Stars 博客上。
您还可以访问我们的内容平台[产品部落](https://producttribe.com/)，它是由专业人士为那些参与产品开发和成长过程的人创建的。

我们随时欢迎您提出问题，分享您想阅读的话题！