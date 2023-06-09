# Healify - Healing songs(第 2 部分)-使用 python 和 flask 与 spotify apis 交互

> 原文：<https://dev.to/gluseppe/healify---healing-songs-part2---interacting-with-spotify-apis-with-python-and-flask-14m1>

你好:)
这是我关于 healify 的日记的第二部分，这是我用来收集治疗歌曲的副业。在进入技术层面之前，我想花些时间感谢所有在阅读了[第一帖](https://dev.to/gluseppe/healing-songs-healing-code-how-im-using-my-side-project-to-heal-part1-46n9)之后，花些时间发首歌和留言的人。谢谢你。目前播放列表中有 92 首歌曲(顺便说一下，我已经添加了一个简单的计数器来显示列表中有多少条目，我对此很自豪，所以去 [healify.it](https://healify.it) 上查看一下)

所以，这部分是关于用 python 建立一个基本的 flask 应用程序，并与 spotify apis 交互。
对于不知道的人来说，flask 是一个“是基于 Werkzeug、Jinja 2 和 good intentions 的 python 的微框架”:)你可以用它在 Python 中构建 REST apis。我喜欢 flask 的一点是，构建和公开函数非常容易。它有很好的例子，如果你看他们的网站，你会发现最简单的应用程序的代码。你首先通过 pip 安装它，然后

```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!" 
```

这是说
1-创建一个 flask 应用程序(flask 应用程序将带有许多用于处理请求的有用方法和对象)
2-当到达“/”路线时，我希望我的应用程序回答“Hello world”。您可以在@app.route decorator 后面为函数使用任何名称

现在，为了在您的开发环境中看到结果，您可以直接运行您的 python 代码，flask 将为您设置一个本地服务器

```
FLASK_APP=hello.py flask run
 * Running on http://localhost:5000/ 
```

这将启动一个简单的内置服务器，您应该只将它用于测试/开发目的。你当然可以使用不同的方式，例如运行我的应用程序，我通常有一个使用 gunicorn 的两行 bash 脚本，如下所示(config.sh 包含我的端口和 app_name，还有我在生产环境中使用的其他变量)

```
#!/bin/bash -
source config.sh
gunicorn -b 127.0.0.1:$port $app_name:app 
```

你可以在这里找到关于[构建一个最小应用的更多细节，在这里](http://flask.pocoo.org/docs/1.0/quickstart/#a-minimal-application)找到关于[部署选项的更多细节](http://flask.pocoo.org/docs/1.0/deploying/#deployment)

让我们继续前进。我们的目标是让我们的代码与 spotify apis 进行交互。特别是，我们希望与搜索节点进行交互，并为自动完成部分构建一个非常简单的引擎(我在 [healify](https://healify.it) 上实现的方式可能不是最好的)。
Spotify 提供了丰富的 api 功能。开始探索它们[这里](https://developer.spotify.com/documentation/web-api/)
你可以使用现有的 spotify 帐户登录，然后你需要通过他们漂亮的仪表板为你的应用程序创建一个客户端 id。这将为您提供一个客户端 ID 和一个客户端密码，这是从您的应用程序验证您的请求所必需的。

与 spotify 交互的一般流程如下
1 -验证您自己并获得一个令牌:您将在以下请求中使用该令牌。根据你下一步想做什么，你将需要不同种类的授权(阅读图书馆，编辑播放列表等)。在我们的例子中，我们需要最低级别，因为我们不会访问任何用户数据
2——向特定节点发送请求并获取数据

我们的节点是[搜索节点](https://developer.spotify.com/documentation/web-api/reference/search/search/)，允许您搜索曲目、专辑、艺术家、播放列表。为此我们需要

*   令牌
*   该查询
*   预期结果的类型(在我们的例子中是轨迹)

首先，我为认证写了一个简单的函数，返回代码

```
def spotify_authenticate(spotify_client_id, spotify_client_sectret):
    data = {'grant_type': 'client_credentials'}
    url = 'https://accounts.spotify.com/api/token'
    response = requests.post(url, data=data, auth=(spotify_client_id, spotify_client_secret))
    return response.json()['access_token'] 
```

然后，我在 flask 应用程序中创建了一个后端搜索分支，如下所示。应该很简单。它通过前面的函数获取令牌，然后在构造头部和参数之后，它使用 requests python 包向 api 节点
发送 GET

```
@app.route('/backend-search', methods=["GET"])
def search():
    token = spotify_authenticate()
    search_url = 'https://api.spotify.com/v1/search'
    #i know i shouldnt be doing this
    search_txt = request.headers.get('search_text','')
    if search_txt == '':
        search_txt = request.args.get('search_text','')

    headers = {
        'Accept': 'application/json',
        'Content-Type': 'application/json',
        'Authorization': 'Bearer {}'.format(token),
    }
    params = (
        ('q', '{}*'.format(search_txt)),
        ('type', 'track'),
        ('limit', 10)
    )

    response = requests.get(search_url, headers=headers, params=params).json()
    return json.dumps(response) 
```

查看响应对象的内部很有趣。它真的包含了很多关于赛道的信息，非常容易理解。试试看，玩得开心一点。
为了测试我的代码，我使用了我的本地服务器和 [Postman](https://www.getpostman.com/) 工具来检查结果。

这部分到此为止。下一个将是关于用语义 ui 构建页面，并通过一些简单的 javascript 发送我们的请求。

敬请关注，不要忘记在 [healify 播放列表](https://healify.it)中添加一些歌曲

谢谢你