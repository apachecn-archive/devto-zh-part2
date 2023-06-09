# 如何整合 Spotify 和 Genius API 用 Python 轻松抓取歌词

> 原文：<https://dev.to/willamesoares/how-to-integrate-spotify-and-genius-api-to-easily-crawl-song-lyrics-with-python-4o62>

[![](img/ddd3e3038c3b7bebc338b2cf41b3dd7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5s4b_9VI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pej1ds486gbxsvesspdt.png)

###### 本帖最初发表于我的个人[博客](http://willamesoares.com)。

大家好！

在这篇文章中，我决定谈谈我去年写的一个 Python 脚本，我现在仍然经常使用它。

这是其中一个时刻，你有一个想法，你真的很兴奋，但不知道它是否会如你所愿。我基本上是从 Python 的一些知识开始的，并且知道我将使用哪个 API 来实现这个想法。

目标是实现一个脚本，我可以用它来获取 Spotify 上当前播放的歌曲的歌词。所以，一开始，我知道我需要一个能为我提供一个接口的东西，我可以用这个接口将脚本与我电脑上运行的 Spotify 服务连接起来。此外，由于我是 Genius.com 的忠实用户，我知道他们有一个大型的公共 API，我可以从中获得一些帮助。

对于 Spotify 的长期用户来说，你可能还记得 Spotify 在桌面应用程序中显示歌词的功能，对吗？美好的旧时光。我仍然不明白为什么它被移除了，但我们现在得到的只是`Behind the Lyrics`功能，它显示了一些~~无用的~~信息和我们正在听的歌曲的歌词片段(似乎它只在移动设备上可用)。

[![](img/c3e0fd3b4c0a6903e37905ace78d04b7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xYILuFo---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/diel5yqu5d7ywmh3lw7l.png)

从这里开始，我将按照脚本实现的顺序开始分解它。如果你想在继续之前检查完整的脚本，你可以去 Github 上的 [this repository](https://github.com/willamesoares/lyrics-crawler) 。在那里，你也会得到如何运行它的指导，但是请记住，我也会在这篇文章中指导你:)

### 连接到 Spotify

这是我不知道如何做的事情之一，所以我必须做一些谷歌搜索来完成它。幸运的是，Stackoverflow 上[joon](https://stackoverflow.com/users/1014870/jooon)的这个[回答](https://stackoverflow.com/questions/33883360/get-spotify-currently-playing-track/33923095#33923095)节省了我很多时间，所以*先生，你真的很了不起*。

基本上，由于我是一个 Linux 用户，我使用的 Spotify 客户端会自动创建一个名为 MPRIS - Media Player 远程接口规范的 D-Bus 接口。

从 [D-Bus](https://dbus.freedesktop.org/doc/dbus-tutorial.html#whatis) 规范页面来看，D-Bus API 通常用于实现将被多个客户端程序消费的服务。这种消息系统是为两种特定情况设计的:同一个桌面会话中的桌面应用程序之间的通信和桌面会话与操作系统之间的通信，后者包括任何系统进程(这是我们的情况)。

一个 D-Bus 系统有几层，其中一层由包装库构成，这就是我们在这里使用的工具。为了让 Python 脚本与 Spotify 桌面应用程序通信，我们将使用 [dbus-python](https://pypi.python.org/pypi/dbus-python/1.2.4) 包装器库，或者技术上称为`libdbus`库的`binding`。将该库安装到您的系统后，您可以使用`dbus`模块创建一个`SessionBus`，我们将使用它与 Spotify 进行通信。

如果你在 Ubuntu 上，由于某种原因，你没有安装`dbus-python`，你可以通过运行`apt-get install python-dbus`
来安装它

```
import dbus

def get_current_song_info():
    # kudos to jooon from this stackoverflow question http://stackoverflow.com/a/33923095
    session_bus = dbus.SessionBus()
    spotify_bus = session_bus.get_object('org.mpris.MediaPlayer2.spotify',
                                         '/org/mpris/MediaPlayer2')
    spotify_properties = dbus.Interface(spotify_bus,
                                        'org.freedesktop.DBus.Properties')
    metadata = spotify_properties.Get('org.mpris.MediaPlayer2.Player', 'Metadata')

    return {'artist': metadata['xesam:artist'][0], 'title': metadata['xesam:title']} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们简单地创建了一个`SessionBus`实例，并用它来创建 Spotify 应用程序的一个`Interface`。

从这个连接中，我们可以提取该会话的元数据，这将为我们提供关于当前 Spotify 会话的大量信息，包括当前播放的艺术家和歌曲的名称，这是我们需要的。注意，这个方法返回一个包含两个键的字典，`artist`和`title`。

### 连接到 Genius API

因为我们已经有了想要搜索的艺术家和歌曲的名字，所以我们可以继续向 Genius API 发出请求。

为此，我们需要一个访问令牌，可以通过创建一个 Genius API 客户端来获得。首先，我们必须[注册一个账户](https://genius.com/signup_or_login)(或者如果你已经在 Genius 上注册了账户，请登录)。之后，您可以进入 Genius API 文档页面，以便[管理您的客户端](https://genius.com/api-clients)。创建新客户端的过程非常简单，之后，您将最终获得您的访问令牌。

一旦您有了访问令牌，我们就可以实现下一个方法。

```
import requests

def request_song_info(song_title, artist_name):
    base_url = 'https://api.genius.com'
    headers = {'Authorization': 'Bearer ' + 'INSERT YOUR TOKEN HERE'}
    search_url = base_url + '/search'
    data = {'q': song_title + ' ' + artist_name}
    response = requests.get(search_url, data=data, headers=headers)

    return response 
```

Enter fullscreen mode Exit fullscreen mode

该方法接收我们从 Spotify 会话中提取的歌曲和艺术家姓名，并向 Genius API 发送请求。注意，我们使用 [requests](http://docs.python-requests.org/en/master/) HTTP 库来发送一个 GET 请求。

### 提取歌曲歌词

如果一切正常，那么现在您将拥有一个响应对象，它包含大量关于在 API 中找到的所有匹配的信息。

由于我们从请求中获得的对象形状，我们将不得不迭代该对象中的`hits`键，并使用`artist_name`变量寻找精确匹配。

```
# Search for matches in the request response
    response = request_song_info(song_title, artist_name)
    json = response.json()
    remote_song_info = None

    for hit in json['response']['hits']:
        if artist_name.lower() in hit['result']['primary_artist']['name'].lower():
            remote_song_info = hit
            break 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在该对象中成功匹配，这意味着我们寻找的歌曲在 API 中可用，并且现在在`remote_song_info`变量中可用。

即使我们有关于一首特定歌曲的所有信息，你猜怎么着，我们没有歌词！！确切地说，Genius API(就这个脚本实现的日期而言)不会直接为您提供歌曲的歌词。也没有搜索歌词的端点。

为了避免这种情况，实际上，我们将不得不抓取播放该歌曲的网页。在`remote_song_info`中，您将获得该歌曲的 URL。

```
# Extract lyrics from URL if the song was found if remote_song_info:
    song_url = remote_song_info['result']['url'] 
```

Enter fullscreen mode Exit fullscreen mode

从这里开始，我们基本上必须让我们的脚本访问页面，并为我们抓取歌词。

```
from bs4 import BeautifulSoup

def scrap_song_url(url):
    page = requests.get(url)
    html = BeautifulSoup(page.text, 'html.parser')
    lyrics = html.find('div', class_='lyrics').get_text()

    return lyrics 
```

Enter fullscreen mode Exit fullscreen mode

该方法接收歌曲 URL，并使用 [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) 库以更简单的方式从 HTML 文件中提取数据。

在实现这个方法的过程中，我必须分析返回的 HTML 的结构，以便找出可以用来提取代表歌曲歌词的文本的结构。我最终使用了包含类`lyrics`的`div`，它保存了所有的歌词。我希望他们永远不会改变那个的标记:)

我们终于可以将歌词打印到控制台上了。酷的是，在爬行过程中，歌词中的每一行都自动接收一个新的行转义序列(`\n`)。这样，我们就可以在终端中获得一个好看的结构。

[![](img/c62145f2800c8a5716304635a314a4bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cLWUG_xo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s5r54yyk1ccl5g0uxz86.png)

### 如何使用 Github 资源库

这篇文章中提到的所有方法都可以在这个[资源库](https://github.com/willamesoares/lyrics-crawler)中找到。

为了在本地使用它，您可以简单地克隆存储库，将您的访问令牌添加到`token.txt`文件中，并且从 repo 文件夹中您可以运行下面的命令来获取一首歌曲的歌词。记得打开 Spotify 放点什么:)

```
python get-lyric.py 
```

Enter fullscreen mode Exit fullscreen mode

该脚本应该适用于 Python 2.7 和 Python 3。但是，您应该检查您正在使用哪个版本，以及您用来安装依赖项的版本。

从存储库中，您可以获得一些额外的功能，例如:

*   自动将歌词保存在`lyric-view.txt`文件中。
*   通过传递艺术家和歌曲名称来搜索任何歌曲(此模式不与 Spotify 交互，因此无需打开它)。查看 [README.md](https://github.com/willamesoares/lyrics-crawler/blob/master/README.md) 。

我想注意的最后一件事是，如果你在你的`.bashrc`文件中为那个命令创建了一个别名，你不必总是在文件夹中获取歌词。

对我来说，我必须将这一行添加到我的`.bashrc`文件中。

```
alias lyric="python ~/repos/lyrics-crawler/get-lyric.py" 
```

Enter fullscreen mode Exit fullscreen mode

此外，我必须在脚本代码中为`token.txt`和`lyric-view.txt`文件使用绝对路径。

有了它，我只需从任何目录运行命令`lyric`,就可以通过 Spotify 获得歌曲的歌词。

我希望这是一本对你有益的读物。如果您对存储库有任何建议或功能，请告诉我或提出请求，我将不胜感激:)

谢谢！