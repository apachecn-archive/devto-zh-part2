# 让我们把一个 Openwhyd 播放列表变成一个静态的 Jekyll 站点

> 原文：<https://dev.to/adrienjoly/lets-turn-an-openwhyd-playlist-into-a-static-jekyll-site-341a>

今天我给自己一个编码挑战:
用 Jekyll 让[我的 openwhyd.org 简介](https://openwhyd.org/adrien)可以从纯客户端 HTML 页面播放(即没有 API/后端服务器)。

# 为什么？

Openwhyd 是一个平台，音乐爱好者可以在这个平台上收集他们在 Youtube、Soundcloud、Bandcamp 和其他几个流媒体平台上找到的歌曲。它允许他们制作跨来源的播放列表，从他们的笔记本电脑或 iPhone 上播放，并与订阅了它们的其他音乐爱好者分享他们的发现。

[![screenshot of my openwhyd profile](img/0b3868336530f670c30f9881eba9d60e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F1JLGtAn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l8kz1srasn617in3brht.png)

在幕后，Openwhyd 是一个由 Node.js 和 MongoDB 支持的 web 应用程序。这意味着我们必须每月向我们的主机提供商支付费用，以保持它在服务器上运行。(仅供参考，这需要 15 美元/月)

到目前为止，我们很幸运得到了数字海洋的赞助:他们给了我们 1 年的免费托管！但这份礼物不会永远持续下去，我们很快就会再次要求用户捐款，以支付托管费用。这对我们和我们的用户来说都不是一件有趣的事情。(见)

这种情况让我思考:**有没有可能继续使用 Openwhyd 而不必支付托管费用？**

由于我是 GitHub 页面的忠实粉丝，我想到的第一个解决方案是去掉 Openwhyd 的后端部分。将它变成一个纯粹的客户端 web 应用程序(也称为静态 web 应用程序)，其中数据库将被用 Jekyll 呈现的数据文件所取代。

当然，如果不使用后端，涵盖 Openwhyd 的所有特性会非常复杂。(例如，从 web 用户界面向播放列表添加曲目，以及从您订阅的用户那里获取最新曲目流)

但我认为，能够播放自己的播放列表是用户实用性和技术复杂性之间的一个很好的折衷。

如果我让这个工作，它将允许一些 Openwhyd 用户仍然能够播放他们花了多年收集的曲目，即使我们的后端有一天会离线。

# 设计技术方案

当用户想要播放他们个人资料或任何播放列表中的歌曲时，会发生以下情况:

1.  他们打开相应 URL 的浏览器标签；(例如[https://openwhyd.org/adrien](https://openwhyd.org/adrien))
2.  后端获取该播放列表的 20 首最新曲目，并呈现包含该曲目列表的 HTML 页面；
3.  用户的浏览器显示页面并加载一堆 JavaScript 文件，特别是`WhydPlayer.js`，它负责使任何列出的曲目都可以播放；
4.  当用户点击其中一个音轨时，`WhydPlayer.js`提取该音轨的源(存储在对应于该音轨的 DOM 节点中)并从那里开始传输；
5.  当到达当前正在播放的曲目末尾时，`WhydPlayer.js`播放下一首曲目；(仍然通过从 DOM 中提取数据)
6.  当到达页面的最后一首曲目时，`WhydPlayer.js`向后端请求下一页曲目，并将它们添加到列表中。

基于这个(简化的)流程，我们可以注意到，后端仅在步骤 2 和 6 中被请求:从播放列表中获取和呈现曲目。剩下的由`WhydPlayer.js`和其他客户端逻辑完成。

第二步可以在 GitHub Pages 这样的静态 web 托管提供商上轻松完成:我们只需将用户的播放列表存储为 HTML 页面。只要 DOM 结构包含流传输每个音轨的音乐所需的所有数据,`WhydPlayer.js`仍然可以使用它。

第 6 步可以变通一下:如果 HTML 页面包含完整的曲目列表，就没有必要获取后续曲目的页面！

现在，让我们提出一个行动计划:

1.  克隆 Openwhyd 的仓库，去掉它的后端文件；
2.  创建一个包含一首曲目的 HTML 页面，并用`WhydPlayer.js`使其可播放；
3.  下载 Openwhyd 简档(或播放列表)的曲目列表；
4.  将 HTML 页面转换为将呈现曲目列表的 Jekyll 模板；
5.  修补 UI，以便用户可以在他们的个人资料和播放列表页面之间导航。

约束:我将尽我所能对 Openwhyd 的代码库(包括它的文件结构)进行尽可能少的修改，以便这些步骤也能在它的未来版本中重现。

> 注意:我是按照这些步骤写这篇文章的。

# 第一步。克隆(并清理)Openwhyd 的存储库

让我们前往 Openwhyd 的官方开源库:[github.com/openwhyd/openwhyd](https://github.com/openwhyd/openwhyd)。

通过“克隆和下载”按钮，git noobs 会很乐意将它下载为一个`zip`文件。专业人士会使用命令行:

```
$ git clone https://github.com/openwhyd/openwhyd.git
$ cd openwhyd 
```

Enter fullscreen mode Exit fullscreen mode

您将在生成的`openwhyd`目录中看到几个文件夹和文件。让我们去掉所有与 Openwhyd 静态版本无关的东西:

```
$ rm Dockerfile
$ rm docker-compose.yml
$ rm -r scripts
$ rm -r whydMisc
$ rm -r whydJS/test
$ rm -r whydJS/config
$ rm -r whydJS/screenshots
$ rm -r whydJS/app/controllers
$ rm -r whydJS/app/data
$ rm -r whydJS/app/emails
$ rm -r whydJS/app/lib
$ rm -r whydJS/app/models
$ rm -r whydJS/app/workers
$ rm -r whydJS/app/views
$ rm whydJS/app/*.*

# delete all files from whydJS/, except package.json
$ find whydJS/* -type f -maxdepth 0 | egrep -v "package\.json" | xargs rm

# delete all folders from public/, except a few ones
$ find whydJS/public/* -maxdepth 0 | egrep -v "css|fonts|html|images|js|swf" | xargs rm -r

# delete all folders from images/
$ find whydJS/publimg/* -type d -maxdepth 0 | xargs rm -r

# delete all files from public/css, except a few ones
$ find whydJS/public/css/* -maxdepth 0 | egrep -v "[^-]common|userPlaylistV2|userProfileV2" | xargs rm

# delete public/html/*, except a few ones
$ find whydJS/public/html/* -maxdepth 0 | egrep -v "channel|\bYoutubePlayerIframe" | xargs rm -r

# delete public/js/*, except a few ones
$ find whydJS/public/js/* -maxdepth 0 | egrep -v "autoresize|\bjquery|mustache|playem|soundmanager|swfobject|ui|whyd\.js|Player\.js" | xargs rm -r

# delete whydJS/app/templates/*, except a few ones
$ find whydJS/app/templates/* -maxdepth 0 | egrep -v "feed\.html|mainTemplate|posts\.html|userPlaylistV2\.html|userProfileV2\.html|whydPlayer" | xargs rm -r

# let's see the tree of remaining folders
$ ls -R | grep ":$" | sed -e 's/:$//' -e 's/[^-][^\/]*\//--/g' -e 's/^/   /' -e 's/-/|/'
   |-docs
   |---img
   |-whydJS
   |---app
   |-----templates
   |---public
   |-----css
   |-----fonts
   |-----html
   |-----images
   |-----js
   |-----swf 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们有更少的文件来分散我们的注意力了！

## 第二步。创建一个带有可播放曲目的 HTML 页面

让我们尝试一个简单的方法:将 openwhyd.org 的播放列表页面保存到一个 HTML 文件中，看看我们能否从中播放一首曲目。

为此，我将在 Google Chrome 上打开[我的个人资料页面](https://openwhyd.org/adrien)，然后按`Cmd-S`将其保存为“简单的 HTML 文件”:

[![saving my openwhyd profile page into a HTML file](img/650c71a56755eea5a29d069e6146432a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6e1pdyUu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m5sliwng4hwy08mpcdm6.png)

当然，如果我试图在我的浏览器中打开那个下载文件，它看起来完全坏了，不可能从中播放曲目。这是因为所有对 JavaScript、CSS 和图像文件的引用都被破坏了。

从生成的 HTML 文件中提取:

```
 Adrien Joly's tracks - Openwhyd
    <link href="/css/common.css?1.1.1" rel="stylesheet" type="text/css" />
    <link href="/css/browse.css?1.1.1" rel="stylesheet" type="text/css" />
    <link href="/css/tipsy.css?1.1.1" rel="stylesheet" type="text/css" />
    <link href="/css/userProfileV2.css?1.1.1" rel="stylesheet" type="text/css" />
    <link href="/css/userPlaylistV2.css?1.1.1" rel="stylesheet" type="text/css" />
    <link href="/css/dlgEditProfileCover.css?1.1.1" rel="stylesheet" type="text/css" />
    <script src="/js/jquery-1.10.2.min.js"></script>
    <script src="/js/jquery-migrate-1.2.1.js"></script>
    <script type="text/javascript" src="/js/jquery.history.js"></script>
    <script src="/js/soundmanager2-nodebug-jsmin.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，该页面使用其所有资产的绝对路径。因此，我们必须在本地设置一个 web 服务器，以便从这些路径访问这些资源。

为了尊重该文件结构，将 HTML 文件移动到`whydJS/public/`文件夹中是有意义的。这样，我们可以认为这个文件夹将成为我们 web 服务器的根目录，使得我们的 HTML 文件和 asset 子文件夹都可以通过它进行访问。

所以我运行下面的命令:

```
# (assuming that you are still in the openwhyd folder we created earlier)
$ cd ./whydJS/public
$ move ~/Downloads/my-openwhyd-profile-playlist.html 
$ python -m SimpleHTTPServer # to start python's http server
Serving HTTP on 0.0.0.0 port 8000 ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们在谷歌浏览器中打开`http://0.0.0.0:8000/my-openwhyd-profile-playlist.html`:

[![screenshot of the HTML page of my openwhyd profile after opening it from my local server](img/95355c1c9fbb4278d77bcc5892f934f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SpnA1YDM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5m42tfhar8yuq293yq9k.png)

正如你所看到的，UI 的一些元素看起来仍然是坏的，但是布局正在工作，你甚至可以通过点击它们来播放一些曲目！

...除了 Youtube 曲目，因为 Openwhyd 的 Youtube 播放器被打了补丁，可以通过另一个域名播放曲目。在我们的例子中，我们不想要这种行为，所以让补丁脚本认为我们正在本地运行(即使我们不是):

```
# let's force `var isLocal = true;`, in whydJS/public/js/playem-youtube-iframe-patch.js
$ sed -i -e "s/var isLocal = /var isLocal = true; /" ./whydJS/public/js/playem-youtube-iframe-patch.js 
```

Enter fullscreen mode Exit fullscreen mode

刷新页面后，这应该会使它工作得更好！

# 3。将曲目列表存储到数据文件中

到目前为止，我们有一个静态的 HTML 页面，其中的曲目是硬编码的。因为我们希望将该页面转换成能够呈现任何播放列表的模板，所以让我们下载一个原始格式的播放列表，而不使用 HTML 呈现。

幸运的是，Openwhyd 使得从任何页面下载 JSON 格式的原始曲目列表变得很容易。我们只需在该页面的 URL 上添加`?format=json`后缀。

在我的例子中，我想要我的个人资料的曲目列表，所以我要下载`https://openwhyd.org/adrien?format=json`的 JSON 内容并保存到`openwhyd/whydJS/public/_data/tracks.json`。

文件的内容应该是这样的:

```
[{"_id":"5b4b6b81314fcb64d074e462","uId":"4d94501d1f78ac091dbc9b4d","uNm":"Adrien Joly","text":"","pl":{"name":"classical & epic","id":1},"name":"Max Richter - November","eId":"/yt/2Bb0k9HgQxc","ctx":"bk","img":"https://i.ytimg.com/vi/2Bb0k9HgQxc/default.jpg","src":{"id":"https://totoromoon.wordpress.com/2018/07/13/max-richter-november-sarajevo/","name":"MAX RICHTER November &amp; Sarajevo | totoromoon"},"nbP":8,"nbR":1,"lov":[]},{"_id":"5b34c9731db44a36a795f96a","uId":"4d94501d1f78ac091dbc9b4d","uNm":"Adrien Joly","text":"","pl":{"name":"epic coding session soundtrack","id":53},"name":"alexanderbrandon - sunrise on vanaar","eId":"/bc/alexanderbrandon/sunrise-on-vanaar#https://t4.bcbits.com/stream/6b5b84ad5900cbb22b532a778f63701d/mp3-128/3200645745?p=0&ts=1530272235&t=01ec41132994dcb46b2a5f5c213a53c39cddb5dc&token=1530272235_39982de236fdfd2e66cf14379f8ba69b44f02f51","ctx":"bk","img":"//s0.bcbits.com/img/bclogo.png","src":{"id":"https://alexanderbrandon.bandcamp.com/album/aven-colony-original-game-soundtrack","name":"▶︎ Aven Colony (Original Game Soundtrack) | Alexander Brandon"},"lov":[]},{"_id":"5b326e261db44a36a795f844","uId":"4d94501d1f78ac091dbc9b4d","uNm":"Adrien Joly","text":"smiling band from cuba, fantastic female drummer !","pl":{"name":"Jazz meets World","id":61},"name":"Yissy García & Bandancha: NPR Music Tiny Desk Concert","eId":"/yt/Y1Kv_sFZHqI","ctx":"bk","img":"https://i.ytimg.com/vi/Y1Kv_sFZHqI/default.jpg","src":{"id":"https://www.youtube.com/watch?v=Y1Kv_sFZHqI&feature=youtu.beq","name":"Yissy García &amp; Bandancha: NPR Music Tiny Desk Concert - YouTube"},"nbP":9,"lov":[]},{"_id":"5b2e34f51db44a36a795f4ff","uId":"4d94501d1f78ac091dbc9b4d","uNm":"Adrien Joly","text":"","pl":{"id":2,"name":"rock","collabId":null},"name":"KADAVAR - Come Back Life (OFFICIAL MUSIC VIDEO)","eId":"/yt/4xgi91s7zf8","img":"https://i.ytimg.com/vi/4xgi91s7zf8/0.jpg","repost":{"pId":"520de33ae4d15ab75a0030f5","uId":"51e92a777e91c862b2af478a","uNm":"Thomas Radioyes"},"lov":[],"nbR":0,"nbP":6},{"_id":"5b2b72c11db44a36a795f361","uId":"4d94501d1f78ac091dbc9b4d","uNm":"Adrien Joly","text":"chiptune","pl":{"name":"epic coding session soundtrack","id":53},"name":"Ujico*/Snail's House - Hello [Ordinary Songs
[...] 
```

Enter fullscreen mode Exit fullscreen mode

随意使用 [JSON Pretty Print](https://jsonformatter.org/json-pretty-print) 重新格式化这个 JSON 文件，或者如果你愿意，使用[json2yaml.com](https://www.json2yaml.com/)将其转换为 YAML，但是这不是 Jekyll 模板工作所必需的。

> 注意:如果你不记得你的 Openwhyd 用户名，你也可以使用 URL `https://openwhyd.org/me?format=json`。

# 4。将 HTML 页面转换成 Jekyll 模板

在第 2 步中，我们让 Openwhyd 的播放器处理静态 HTML 文件中的曲目列表。现在，让我们将这个 HTML 文件转换成一个模板，这样就可以从我们的 JSON 文件中加载曲目列表。(就像我们在之前的教程中所做的一样:[如何使用 Jekyll 和 Github 页面在线维护音乐专辑集](https://dev.to/adrienjoly/how-to-maintain-a-collection-of-music-albums-online-using-jekyll-and-github-pages-3hd6)

基本上，我们要做的是从 HTML 文件中删除代表曲目的元素，并用 Jekyll 模板替换它们。

在 Openwhyd 中，使用带有`post`类名:
的`<div>`元素来显示轨迹

```
# (assuming that you are in the openwhyd/whydJS/public, where your HTML file is)
$ grep '<div class="post' *.html

                <div class="posts">
<div class="post " data-pid="5b546297314fcb64d074e8d3"
<div class="post " data-pid="5b4b6b81314fcb64d074e462"
<div class="post " data-pid="5b34c9731db44a36a795f96a"
[...] 
```

Enter fullscreen mode Exit fullscreen mode

首先，使用我们最喜欢的代码编辑器，让我们将所有这些`<div>`元素剪切并粘贴到一个单独的 HTML 文件(例如`posts.html`)中，这样我们就可以在编写 Jekyll 模板时使用这些 HTML 代码作为示例。

[![moving post divs from the playlist's html file to an external file](img/c5c733993e4af5d4d0b7f8ffd9a28be1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P4RQYge4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7nb38i0vsm1o4fnzkpls.png)

完成这个操作后，你的播放列表的 HTML 文件应该包含一个`<div class="posts">`元素，这个元素只包含了`<!-- TRACKS TAB CONTENT -->`注释。这就是我们现在要添加 Jekyll 模板的地方。

为了更容易地构建模板，让我们复制一个从 HTML 文件中移出的帖子的代码，除了具有类`ago`、`stats`、`btns`和`ext`的子节点，回到`<div class="posts">`元素下。

重新格式化 HTML 后，它应该看起来像这样:

```
<div class="posts">

  <!-- TRACKS TAB CONTENT -->

  <div class="post "
        data-pid="5b546297314fcb64d074e8d3"
        data-initialpid="5b540c4d314fcb64d074e891" 
        data-time="1532256919000" >

    <div class="playBar"></div>

    <a class="thumb"
        href="//youtube.com/watch?v=u8rVPIV-Y4Y"
        target="_blank"
        data-eid="/yt/u8rVPIV-Y4Y"
        onclick="return playTrack(this);"
        style="background-image:url('https://i.ytimg.com/vi/u8rVPIV-Y4Y/default.jpg');">
      <img src="https://i.ytimg.com/vi/u8rVPIV-Y4Y/default.jpg">
      <div class="play"></div>
    </a>

    <h2>
      <a href="/c/5b546297314fcb64d074e8d3"
          class="no-ajaxy"
          onclick="toggleComments('5b546297314fcb64d074e8d3');return false;">
          Paraphon Tree - Firefly (Official Music Video)</a>
    </h2>

    <p class="author">
      <span style="background-image:url('/img/u/4d94501d1f78ac091dbc9b4d');"></span>
      <a href="/u/4d94501d1f78ac091dbc9b4d">Adrien Joly</a> added this track
      to <a href="/u/4d94501d1f78ac091dbc9b4d/playlist/12">post-rock / progressive</a>
      via <a href="/u/544c39c3e04b7b4fca803438">Stefanos Mavrogenis</a>
    </p>
  </div>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们把这个样本帖子变成一个 Jekyll 模板，可以用来渲染任何轨道。

为了做到这一点，我们用*小胡子*占位符替换所有硬编码的值，这些占位符包含来自我们的`tracks.json`文件的任何轨道中的值的路径。

例如，我替换下面的 HTML 代码:

```
<a href="/u/4d94501d1f78ac091dbc9b4d">Adrien Joly</a> added this track
to <a href="/u/4d94501d1f78ac091dbc9b4d/playlist/12">post-rock / progressive</a> 
```

Enter fullscreen mode Exit fullscreen mode

...按此模板编码:

```
<a href="/profile">{{ track.uNm }}</a> added this track
to <a href="/playlist-{{ track.pl.id }}">{{ track.pl.name }}</a> 
```

Enter fullscreen mode Exit fullscreen mode

应该是这样结束的:

```
<div class="posts">

  <!-- TRACKS TAB CONTENT -->

  {% for track in site.data.tracks %}
  <div class="post"
        data-pid="{{ track._id }}"
        data-initialpid="{{ track.repost.pId }}">

    <div class="playBar"></div>

    <a class="thumb"
        href="#"
        data-eid="{{ track.eId }}"
        onclick="return playTrack(this);"
        style="background-image:url('{{ track.img }}');">
      <img src="{{ track.img }}">
      <div class="play"></div>
    </a>

    <h2>
      <a href="#">{{ track.name }}</a>
    </h2>

    <p class="author">
      <span style="background-image:url('{{ user.img }}');"></span>
      <a href="/profile">{{ track.uNm }}</a> added this track
      to <a href="/playlist-{{ track.pl.id }}">{{ track.pl.name }}</a>
      via <a href="https://openwhyd.org/u/{{ track.repost.uId }}">{{ track.repost.uNm  }}</a>
    </p>
  </div>
  {% endfor %}

</div> 
```

Enter fullscreen mode Exit fullscreen mode

为了测试这个模板的渲染，我们上面跑的`python -m SimpleHTTPServer`也无济于事。所以还是别说了(按`Ctrl-C`)用 Jekyll 代替吧。

下面是我如何从我的`openwhyd/whydJS/public/`文件夹:
中设置 Jekyll

```
# let's make sure we have ruby installed
$ ruby -v
ruby 2.3.7p456 (2018-03-28 revision 63024) [x86_64-darwin15]

# let's install jekyll in openwhyd/whydJS/public/
$ bundle init
$ bundle install --path vendor/bundle
$ bundle add jekyll
$ bundle install

# this command installs jekyll in ./vendor/bundle => let's git-ignore it (optional)
$ echo /whydJS/public/vendor >>../../.gitignore
$ echo /whydJS/public/_site >>../../.gitignore 
```

Enter fullscreen mode Exit fullscreen mode

当它完全设置好后，我们可以通过输入以下命令来运行 Jekyll 服务器:

```
$ bundle exec jekyll serve
    Server address: http://127.0.0.1:4000
  Server running... press ctrl-c to stop. 
```

Enter fullscreen mode Exit fullscreen mode

我们来开`http://127.0.0.1:4000/my-openwhyd-profile-playlist.html`。

[![screenshot of the Jekyll template failing to render](img/c50c5688c1b2c2a43e8c3a6d551f220a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uxROQApX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dff1qzrwhpfr374d26j8.png)

好消息是我们的页面被 Jekyll 很好地服务了。坏消息是我们的模板无法渲染。看起来 Jekyll 没有解释 HTML 文件中的模板。

我上网查了一下 Jekyll 渲染模板的流程(和标准),发现了这个:

> Jekyll 查找带有 front matter 标签的文件(两组虚线——就像 index.md 中的那些),并处理这些文件(填充站点变量，呈现任何液体，并将 Markdown 转换为 HTML)。

(来源:[将一个 HTML 站点转换成 Jekyll | Jekyll](https://jekyllrb.com/tutorials/convert-site-to-jekyll/) )

因此，让我们在 HTML 文件的顶部添加两组虚线:

```
---
---
<!DOCTYPE html>
<html lang="en">
[...] 
```

Enter fullscreen mode Exit fullscreen mode

正常情况下，`jekyll serve`命令应该已经检测到这种修改，并且当在 web 浏览器中打开/刷新`http://127.0.0.1:4000/my-openwhyd-profile-playlist.html`时，曲目列表应该从 JSON 文件中正确地呈现出来！

[![jekyll is rendering the track list from our JSON file](img/8a9d218e2425d4012a8d14b4e5983ed4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5HCCyPRH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rkfj96tx0uikhlugsytu.png)

干得好！

# 下一步

我现在已经完成了我今天的目标:我们将一个 Openwhyd 概要文件变成了一个静态的 Jekyll 站点。曲目列表是以 JSON 格式下载的，Jekyll 使用它按照我们编写的模板将其呈现为 HTML。

下一步将是修复导航，这样我的 Openwhyd 配置文件的静态版本的访问者也可以浏览我的播放列表，遵循同样的原则。

为了做到这一点，我打算使用 [Jekyll 集合](https://jekyllrb.com/docs/collections)。

在我继续写这篇文章的续集之前，如果你对如何完成这篇文章有其他想法，请在评论中告诉我！