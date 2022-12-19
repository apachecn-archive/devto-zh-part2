# 用 Python 和 Vue 创建一个双人游戏

> 原文：<https://dev.to/neo/create-a-two-player-game-with-python-and-vue-4c3j>

> 你需要在你的机器上安装 Python 3+，virtualenv 和 Flask。

个人电脑和互联网的出现重新定义了“娱乐”一词以及获得娱乐的方式。虽然在过去玩游戏需要一台游戏机或一些特殊的硬件，但在当今的科技世界中，游戏只需点击一下鼠标。

在本教程中，我们将使用 Python 和 Pusher 通道创建一个实时井字游戏。这是一个游戏创建后的外观和行为的演示:

[![](../Images/8ca1e1d194a0427324c093d10ce91d2f.png)T2】](//images.ctfassets.net/1es3ne0caaid/1RGRfjXfgQgYWYy2kqImOs/0c1db39f9371dbb773638ffe57bfd385/python-vue-game-demo.gif)

这种多人游戏将允许玩家使用他们首选的用户名连接(或者在玩家没有使用用户名连接的情况下生成随机用户名)，并从其他在线玩家列表中选择与另一个玩家一起玩。

游戏本身遵循了流行的井字游戏的常规原则。“在线玩家”功能由[推送器存在通道](https://pusher.com/docs/client_api_guide/client_presence_channels)提供支持，玩家跨多个窗口移动的实时更新由[推送器私人通道提供支持。](https://pusher.com/docs/client_api_guide/client_private_channels)本教程的源代码可以在这里找到 [GitHub](https://github.com/neoighodaro/python-pusher-multiplayer-game) 。

让我们开始吧。

## 先决条件

为了跟上，需要 Python、Flask、JavaScript (ES6 语法)和 Vue 的基础知识。您还需要在计算机上安装以下软件:

1.  [Python (v3.x)](https://www.python.org/)
2.  [虚拟人](https://virtualenv.pypa.io/en/stable/)
3.  [烧瓶](http://flask.pocoo.org/)

Virtualenv 非常适合创建隔离的 Python 环境，因此我们可以在隔离的环境中安装依赖项，而不会污染我们的全局包目录。

## 设置 app 环境

我们将创建项目文件夹并在其中激活一个虚拟环境:

```
 $ mkdir python-pusher-mutiplayer-game
    $ cd python-pusher-mutiplayer-game
    $ virtualenv .venv
    $ source .venv/bin/activate # Linux based systems
    $ \path\to\env\Scripts\activate # Windows users 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用命令
安装[烧瓶](http://flask.pocoo.org/)

```
 $ pip install flask 
```

Enter fullscreen mode Exit fullscreen mode

## 设置推动器

为了将 Pusher 集成到多人游戏中，我们需要从 Pusher 仪表盘创建一个 Pusher channels 应用程序。如果你还没有一个推销者帐户，请到[推销者网站](https://pusher.com/)创建一个。

创建帐户后，创建一个新的 channels 应用程序，并从应用程序仪表板中启用客户端事件。要启用客户端事件，点击**应用设置**并滚动到页面底部，然后选择选项**启用客户端事件，**并更新**应用设置。**

## 构建后端服务器

回到项目目录，让我们用命令
安装 [Python Pusher 库](https://github.com/pusher/pusher-http-python)

```
 $ pip install pusher 
```

Enter fullscreen mode Exit fullscreen mode

我们将创建一个新文件，并将其命名为`app.py`，这是我们将为 Flask 后端服务器编写所有代码的地方。我们还将创建一个文件夹，并将其命名为`templates`，该文件夹将保存该应用程序的标记文件。

让我们编写一些代码来注册游戏的端点并为视图提供服务，打开`app.py`文件并粘贴以下代码:

```
 // File: ./app.py
    <span class="hljs-keyword">from</span> flask <span class="hljs-keyword">import</span> Flask, render_template, request, jsonify, make_response, json
    <span class="hljs-keyword">from</span> pusher <span class="hljs-keyword">import</span> pusher

    app = Flask(__name__)

    pusher = pusher_client = pusher.Pusher(
      app_id=<span class="hljs-string">'PUSHER_APP_ID'</span>,
      key=<span class="hljs-string">'PUSHER_APP_KEY'</span>,
      secret=<span class="hljs-string">'PUSHER_APP_SECRET'</span>,
      cluster=<span class="hljs-string">'PUSHER_APP_CLUSTER'</span>,
      ssl=<span class="hljs-keyword">True</span>
    )

    name = <span class="hljs-string">''</span>

<span class="hljs-meta">    @app.route('/')</span>
    <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">index</span><span class="hljs-params">()</span>:</span>
      <span class="hljs-keyword">return</span> render_template(<span class="hljs-string">'index.html'</span>)

<span class="hljs-meta">    @app.route('/play')</span>
    <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">play</span><span class="hljs-params">()</span>:</span>
      <span class="hljs-keyword">global</span> name
      name = request.args.get(<span class="hljs-string">'username'</span>)
      <span class="hljs-keyword">return</span> render_template(<span class="hljs-string">'play.html'</span>)

<span class="hljs-meta">    @app.route("/pusher/auth", methods=['POST'])</span>
    <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">pusher_authentication</span><span class="hljs-params">()</span>:</span>
      auth = pusher.authenticate(
        channel=request.form[<span class="hljs-string">'channel_name'</span>],
        socket_id=request.form[<span class="hljs-string">'socket_id'</span>],
        custom_data={
          <span class="hljs-string">u'user_id'</span>: name,
          <span class="hljs-string">u'user_info'</span>: {
            <span class="hljs-string">u'role'</span>: <span class="hljs-string">u'player'</span>
          }
        }
      )
      <span class="hljs-keyword">return</span> json.dumps(auth)

    <span class="hljs-keyword">if</span> __name__ == <span class="hljs-string">'__main__'</span>:
        app.run(host=<span class="hljs-string">'0.0.0.0'</span>, port=<span class="hljs-number">5000</span>, debug=<span class="hljs-keyword">True</span>)

    name = <span class="hljs-string">''</span> 
```

Enter fullscreen mode Exit fullscreen mode

> 用推动器仪表板上的值替换`PUSHER_APP_*`键。

在上面的代码中，我们定义了三个端点，它们的作用如下:

*   呈现要求玩家用用户名连接的首页。
*   `/play` -渲染游戏视图。
*   `/pusher/auth` -为连接的玩家验证出牌者的存在和私人通道。

## 建筑前端

在`templates`文件夹中，我们将创建两个文件:

1.  `index.html`
2.  `play.html`

`index.html`文件将呈现连接页面，因此打开`templates/index.html`文件并粘贴以下代码:

```
<span class="hljs-comment"></span>
<span class="hljs-meta"></span>
<span class="hljs-tag"><<span class="hljs-name">html</span> <span class="hljs-attr">lang</span>=<span class="hljs-string">"en"</span>></span>
    <span class="hljs-tag"><<span class="hljs-name">head</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">charset</span>=<span class="hljs-string">"utf-8"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">name</span>=<span class="hljs-string">"viewport"</span> <span class="hljs-attr">content</span>=<span class="hljs-string">"width=device-width, initial-scale=1, shrink-to-fit=no"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">name</span>=<span class="hljs-string">"description"</span> <span class="hljs-attr">content</span>=<span class="hljs-string">""</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">name</span>=<span class="hljs-string">"author"</span> <span class="hljs-attr">content</span>=<span class="hljs-string">"Neo Ighodaro"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">title</span>></span>TIC-TAC-TOE<span class="hljs-tag"></<span class="hljs-name">title</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">link</span> <span class="hljs-attr">rel</span>=<span class="hljs-string">"stylesheet"</span> <span class="hljs-attr">href</span>=<span class="hljs-string">"https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">style</span>></span><span class="css">
              <span class="hljs-selector-pseudo">:root</span> {
                <span class="hljs-attribute">--input-padding-x</span>: .<span class="hljs-number">75rem</span>;
                <span class="hljs-attribute">--input-padding-y</span>: .<span class="hljs-number">75rem</span>;
              }
              <span class="hljs-selector-tag">html</span>,
              <span class="hljs-selector-tag">body</span>, <span class="hljs-selector-tag">body</span> > <span class="hljs-selector-tag">div</span> {
                <span class="hljs-attribute">height</span>: <span class="hljs-number">100%</span>;
              }
              <span class="hljs-selector-tag">body</span> > <span class="hljs-selector-tag">div</span> {
                <span class="hljs-attribute">display</span>: -ms-flexbox;
                <span class="hljs-attribute">display</span>: flex;
                <span class="hljs-attribute">-ms-flex-align</span>: center;
                <span class="hljs-attribute">align-items</span>: center;
                <span class="hljs-attribute">padding-top</span>: <span class="hljs-number">40px</span>;
                <span class="hljs-attribute">padding-bottom</span>: <span class="hljs-number">40px</span>;
                <span class="hljs-attribute">background-color</span>: <span class="hljs-number">#f5f5f5</span>;
              }
              <span class="hljs-selector-class">.form-signin</span> {
                <span class="hljs-attribute">width</span>: <span class="hljs-number">100%</span>;
                <span class="hljs-attribute">max-width</span>: <span class="hljs-number">420px</span>;
                <span class="hljs-attribute">padding</span>: <span class="hljs-number">15px</span>;
                <span class="hljs-attribute">margin</span>: auto;
              }
              <span class="hljs-selector-class">.form-label-group</span> {
                <span class="hljs-attribute">position</span>: relative;
                <span class="hljs-attribute">margin-bottom</span>: <span class="hljs-number">1rem</span>;
              }
              <span class="hljs-selector-class">.form-label-group</span> > <span class="hljs-selector-tag">input</span>,
              <span class="hljs-selector-class">.form-label-group</span> > <span class="hljs-selector-tag">label</span> {
                <span class="hljs-attribute">padding</span>: <span class="hljs-built_in">var</span>(--input-padding-y) <span class="hljs-built_in">var</span>(--input-padding-x);
              }
              <span class="hljs-selector-class">.form-label-group</span> > <span class="hljs-selector-tag">label</span> {
                <span class="hljs-attribute">position</span>: absolute;
                <span class="hljs-attribute">top</span>: <span class="hljs-number">0</span>;
                <span class="hljs-attribute">left</span>: <span class="hljs-number">0</span>;
                <span class="hljs-attribute">display</span>: block;
                <span class="hljs-attribute">width</span>: <span class="hljs-number">100%</span>;
                <span class="hljs-attribute">margin-bottom</span>: <span class="hljs-number">0</span>; <span class="hljs-comment">/* Override default `<label>` margin */</span>
                <span class="hljs-attribute">line-height</span>: <span class="hljs-number">1.5</span>;
                <span class="hljs-attribute">color</span>: <span class="hljs-number">#495057</span>;
                <span class="hljs-attribute">cursor</span>: text; <span class="hljs-comment">/* Match the input under the label */</span>
                <span class="hljs-attribute">border</span>: <span class="hljs-number">1px</span> solid transparent;
                <span class="hljs-attribute">border-radius</span>: .<span class="hljs-number">25rem</span>;
                <span class="hljs-attribute">transition</span>: all .<span class="hljs-number">1s</span> ease-in-out;
              }
              <span class="hljs-selector-class">.form-label-group</span> <span class="hljs-selector-tag">input</span><span class="hljs-selector-pseudo">::-webkit-input-placeholder</span> {
                <span class="hljs-attribute">color</span>: transparent;
              }
              <span class="hljs-selector-class">.form-label-group</span> <span class="hljs-selector-tag">input</span><span class="hljs-selector-pseudo">:-ms-input-placeholder</span> {
                <span class="hljs-attribute">color</span>: transparent;
              }
              <span class="hljs-selector-class">.form-label-group</span> <span class="hljs-selector-tag">input</span><span class="hljs-selector-pseudo">::-ms-input-placeholder</span> {
                <span class="hljs-attribute">color</span>: transparent;
              }
              <span class="hljs-selector-class">.form-label-group</span> <span class="hljs-selector-tag">input</span><span class="hljs-selector-pseudo">::-moz-placeholder</span> {
                <span class="hljs-attribute">color</span>: transparent;
              }
              <span class="hljs-selector-class">.form-label-group</span> <span class="hljs-selector-tag">input</span><span class="hljs-selector-pseudo">::placeholder</span> {
                <span class="hljs-attribute">color</span>: transparent;
              }
              <span class="hljs-selector-class">.form-label-group</span> <span class="hljs-selector-tag">input</span><span class="hljs-selector-pseudo">:not(</span><span class="hljs-selector-pseudo">:placeholder-shown)</span> {
                <span class="hljs-attribute">padding-top</span>: <span class="hljs-built_in">calc</span>(var(--input-padding-y) + <span class="hljs-built_in">var</span>(--input-padding-y) * (<span class="hljs-number">2</span> / <span class="hljs-number">3</span>));
                <span class="hljs-attribute">padding-bottom</span>: <span class="hljs-built_in">calc</span>(var(--input-padding-y) / <span class="hljs-number">3</span>);
              }
              <span class="hljs-selector-class">.form-label-group</span> <span class="hljs-selector-tag">input</span><span class="hljs-selector-pseudo">:not(</span><span class="hljs-selector-pseudo">:placeholder-shown)</span> ~ <span class="hljs-selector-tag">label</span> {
                <span class="hljs-attribute">padding-top</span>: <span class="hljs-built_in">calc</span>(var(--input-padding-y) / <span class="hljs-number">3</span>);
                <span class="hljs-attribute">padding-bottom</span>: <span class="hljs-built_in">calc</span>(var(--input-padding-y) / <span class="hljs-number">3</span>);
                <span class="hljs-attribute">font-size</span>: <span class="hljs-number">12px</span>;
                <span class="hljs-attribute">color</span>: <span class="hljs-number">#777</span>;
              }
        </span><span class="hljs-tag"></<span class="hljs-name">style</span>></span>
      <span class="hljs-tag"></<span class="hljs-name">head</span>></span>
      <span class="hljs-tag"><<span class="hljs-name">body</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"app"</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">form</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"form-signin"</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"text-center mb-4"</span>></span>
              <span class="hljs-tag"><<span class="hljs-name">img</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"mb-4"</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"https://thestore.gameops.com/v/vspfiles/photos/Tic-Tac-Go-14.gif"</span> <span class="hljs-attr">alt</span>=<span class="hljs-string">""</span> <span class="hljs-attr">width</span>=<span class="hljs-string">"72"</span> <span class="hljs-attr">height</span>=<span class="hljs-string">"72"</span>></span>
              <span class="hljs-tag"><<span class="hljs-name">h1</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"h3 mb-3 font-weight-normal"</span>></span>TIC-TAC-TOE<span class="hljs-tag"></<span class="hljs-name">h1</span>></span>
              <span class="hljs-tag"><<span class="hljs-name">p</span>></span>PUT IN YOUR DETAILS TO PLAY<span class="hljs-tag"></<span class="hljs-name">p</span>></span>
            <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"form-label-group"</span>></span>
                <span class="hljs-tag"><<span class="hljs-name">input</span> <span class="hljs-attr">type</span>=<span class="hljs-string">"name"</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"inputUsername"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"username"</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"form-control"</span> <span class="hljs-attr">placeholder</span>=<span class="hljs-string">"Username"</span> <span class="hljs-attr">required</span>=<span class="hljs-string">""</span> <span class="hljs-attr">autofocus</span>=<span class="hljs-string">""</span>></span>
                  <span class="hljs-tag"><<span class="hljs-name">label</span> <span class="hljs-attr">for</span>=<span class="hljs-string">"inputUsername"</span>></span>Username<span class="hljs-tag"></<span class="hljs-name">label</span>></span>
            <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"form-label-group"</span>></span>
              <span class="hljs-tag"><<span class="hljs-name">input</span> <span class="hljs-attr">type</span>=<span class="hljs-string">"email"</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"inputEmail"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"email"</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"form-control"</span> <span class="hljs-attr">placeholder</span>=<span class="hljs-string">"Email address"</span> <span class="hljs-attr">autofocus</span>=<span class="hljs-string">""</span> <span class="hljs-attr">required</span>></span>
                <span class="hljs-tag"><<span class="hljs-name">label</span> <span class="hljs-attr">for</span>=<span class="hljs-string">"inputEmail"</span>></span>Email address<span class="hljs-tag"></<span class="hljs-name">label</span>></span>
            <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">button</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"btn btn-lg btn-primary btn-block"</span> <span class="hljs-attr">type</span>=<span class="hljs-string">"submit"</span> @<span class="hljs-attr">click.prevent</span>=<span class="hljs-string">"login"</span>></span>Connect<span class="hljs-tag"></<span class="hljs-name">button</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">p</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"mt-5 mb-3 text-muted text-center"</span>></span>© 2017-2018<span class="hljs-tag"></<span class="hljs-name">p</span>></span>
          <span class="hljs-tag"></<span class="hljs-name">form</span>></span>
        <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">script</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"https://cdn.jsdelivr.net/npm/vue/dist/vue.js"</span>></span><span class="undefined"></span><span class="hljs-tag"></<span class="hljs-name">script</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">script</span>></span><span class="javascript">
        <span class="hljs-keyword">var</span> app = <span class="hljs-keyword">new</span> Vue({
          <span class="hljs-attr">el</span>: <span class="hljs-string">'#app'</span>,
          <span class="hljs-attr">methods</span>: {
            <span class="hljs-attr">login</span>: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
              <span class="hljs-keyword">let</span> username = <span class="hljs-keyword">this</span>.$refs.username.value
              <span class="hljs-keyword">let</span> email = <span class="hljs-keyword">this</span>.$refs.email.value
              <span class="hljs-built_in">window</span>.location.replace(<span class="hljs-string">`/play?username=<span class="hljs-subst">${username}</span>&email=<span class="hljs-subst">${email}</span>`</span>);
            }
          }
        })
        </span><span class="hljs-tag"></<span class="hljs-name">script</span>></span>
    <span class="hljs-tag"></<span class="hljs-name">body</span>></span>
<span class="hljs-tag"></<span class="hljs-name">html</span>></span> 
```

Enter fullscreen mode Exit fullscreen mode

当玩家访问连接页面并输入用户名和电子邮件时，浏览器窗口将被重定向到游戏视图。

让我们为游戏视图编写标记。打开`play.html`文件并粘贴以下代码:

```
<span class="hljs-comment"></span>
<span class="hljs-meta"></span>
<span class="hljs-tag"><<span class="hljs-name">html</span> <span class="hljs-attr">lang</span>=<span class="hljs-string">"en"</span>></span>
<span class="hljs-tag"><<span class="hljs-name">head</span>></span>
  <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">charset</span>=<span class="hljs-string">"utf-8"</span>></span>
  <span class="hljs-tag"><<span class="hljs-name">meta</span> <span class="hljs-attr">name</span>=<span class="hljs-string">"viewport"</span> <span class="hljs-attr">content</span>=<span class="hljs-string">"width=device-width, initial-scale=1, shrink-to-fit=no"</span>></span>
  <span class="hljs-tag"><<span class="hljs-name">link</span> <span class="hljs-attr">rel</span>=<span class="hljs-string">"stylesheet"</span> <span class="hljs-attr">href</span>=<span class="hljs-string">"https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css"</span>></span>
  <span class="hljs-tag"><<span class="hljs-name">title</span>></span>TIC-TAC-TOE<span class="hljs-tag"></<span class="hljs-name">title</span>></span>
<span class="hljs-tag"></<span class="hljs-name">head</span>></span>
<span class="hljs-tag"><<span class="hljs-name">body</span>></span>
  <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">id</span>=<span class="hljs-string">"app"</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"container-fluid"</span>></span>
    <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"container-fluid clearfix mb-3 shadow"</span>></span>
      <span class="hljs-tag"><<span class="hljs-name">img</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"float-left my-3"</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"https://thestore.gameops.com/v/vspfiles/photos/Tic-Tac-Go-14.gif"</span> <span class="hljs-attr">height</span>=<span class="hljs-string">"62px"</span> <span class="hljs-attr">width</span>=<span class="hljs-string">"62px"</span>
      /></span>
      <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"float-right w-25 py-3"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">img</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"my-3 mx-3 rounded-circle border"</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"http://dfsanonymous.club/wp-content/uploads/2017/11/DFSAnonymous-NewLogo.png"</span>
          <span class="hljs-attr">height</span>=<span class="hljs-string">"62px"</span> <span class="hljs-attr">width</span>=<span class="hljs-string">"62px"</span> /></span>
        <span class="hljs-tag"><<span class="hljs-name">p</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"d-inline"</span>></span>  {{ username }}  <span class="hljs-tag"></<span class="hljs-name">p</span>></span>
      <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
    <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
    <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"row mx-5"</span> <span class="hljs-attr">style</span>=<span class="hljs-string">"height: 50vh"</span>></span>
      <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col-8 h-50 align-self-center"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"row border rounded invisible h-50 w-75 m-auto"</span> <span class="hljs-attr">style</span>=<span class="hljs-string">"font-size: 3.6rem"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"gameboard"</span> @<span class="hljs-attr">click</span>=<span class="hljs-string">"playerAction"</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"h-100 pr-2 col border border-dark"</span> <span class="hljs-attr">data-id</span>=<span class="hljs-string">"1"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"1"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col pr-2 border border-dark"</span> <span class="hljs-attr">data-id</span>=<span class="hljs-string">"2"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"2"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col pr-2 border border-dark"</span> <span class="hljs-attr">data-id</span>=<span class="hljs-string">"3"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"3"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"w-100"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"h-100 pr-2 col border border-dark"</span> <span class="hljs-attr">data-id</span>=<span class="hljs-string">"4"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"4"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col pr-2 border border-dark"</span> <span class="hljs-attr">data-id</span>=<span class="hljs-string">"5"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"5"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col pr-2 border border-dark"</span> <span class="hljs-attr">data-id</span>=<span class="hljs-string">"6"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"6"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"w-100"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"h-100 pr-2 col border border-dark"</span> <span class="hljs-attr">data-id</span>=<span class="hljs-string">"7"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"7"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col pr-2 border border-dark"</span> <span class="hljs-attr">data-id</span>=<span class="hljs-string">"8"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"8"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col pr-2 border border-dark"</span> <span class="hljs-attr">data-id</span>=<span class="hljs-string">"9"</span> <span class="hljs-attr">ref</span>=<span class="hljs-string">"9"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
        <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
      <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
      <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col-4 pl-3"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"row h-100"</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col border h-75 text-center"</span> <span class="hljs-attr">style</span>=<span class="hljs-string">"background: rgb(114, 230, 147);"</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">p</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"my-3"</span>></span>  {{ players }}  online player(s) <span class="hljs-tag"></<span class="hljs-name">p</span>></span>
            <span class="hljs-tag"><<span class="hljs-name">hr</span>/></span>
            <span class="hljs-tag"><<span class="hljs-name">li</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"m-auto py-3 text-dark"</span> <span class="hljs-attr">style</span>=<span class="hljs-string">"cursor: pointer;"</span> <span class="hljs-attr">v-for</span>=<span class="hljs-string">"member in connectedPlayers"</span> @<span class="hljs-attr">click</span>=<span class="hljs-string">"choosePlayer"</span>></span>
               {{ member }} 
            <span class="hljs-tag"></<span class="hljs-name">li</span>></span>
          <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"w-100"</span>></span><span class="hljs-tag"></<span class="hljs-name">div</span>></span>
          <span class="hljs-tag"><<span class="hljs-name">div</span> <span class="hljs-attr">class</span>=<span class="hljs-string">"col text-center py-3 border h-25"</span> <span class="hljs-attr">style</span>=<span class="hljs-string">"background: #b6c0ca; font-size: 1em; font-weight: bold"</span>></span>
             {{ status }} 
          <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
        <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
      <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
    <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
  <span class="hljs-tag"></<span class="hljs-name">div</span>></span>
  <span class="hljs-tag"><<span class="hljs-name">script</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"https://cdn.jsdelivr.net/npm/vue/dist/vue.js"</span>></span><span class="undefined"></span><span class="hljs-tag"></<span class="hljs-name">script</span>></span>
  <span class="hljs-tag"><<span class="hljs-name">script</span> <span class="hljs-attr">src</span>=<span class="hljs-string">"https://js.pusher.com/4.2/pusher.min.js"</span>></span><span class="undefined"></span><span class="hljs-tag"></<span class="hljs-name">script</span>></span>
  <span class="hljs-tag"><<span class="hljs-name">script</span>></span><span class="undefined">

  </span><span class="hljs-tag"></<span class="hljs-name">script</span>></span>
<span class="hljs-tag"></<span class="hljs-name">body</span>></span>
<span class="hljs-tag"></<span class="hljs-name">html</span>></span> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码定义了游戏视图的布局，但不包含任何交互性或实时特性。在脚本部分，在结束的`body`标签之前，我们包含了 Vue 和 Pusher 库，因为它们是游戏运行所必需的。

让我们包括驱动整个游戏过程并定义其逻辑的 JavaScript 代码。

在同一个文件中，将下面的代码添加到位于结束的`body`标记之前的`script`标记之间:

```
<span class="hljs-keyword">var</span> app = <span class="hljs-keyword">new</span> Vue({
  <span class="hljs-attr">el</span>: <span class="hljs-string">'#app'</span>,

  <span class="hljs-attr">data</span>: {
    <span class="hljs-attr">username</span>: <span class="hljs-string">''</span>,
    <span class="hljs-attr">players</span>: <span class="hljs-number">0</span>,
    <span class="hljs-attr">connectedPlayers</span>: [],
    <span class="hljs-attr">status</span>: <span class="hljs-string">''</span>,
    <span class="hljs-attr">pusher</span>: <span class="hljs-keyword">new</span> Pusher(<span class="hljs-string">'PUSHER_APP_KEY'</span>, {
      <span class="hljs-attr">authEndpoint</span>: <span class="hljs-string">'/pusher/auth'</span>,
      <span class="hljs-attr">cluster</span>: <span class="hljs-string">'PUSHER_APP_CLUSTER'</span>,
      <span class="hljs-attr">encrypted</span>: <span class="hljs-literal">true</span>
    }),
    <span class="hljs-attr">otherPlayerName</span>: <span class="hljs-string">''</span>,
    <span class="hljs-attr">mychannel</span>: {},
    <span class="hljs-attr">otherPlayerChannel</span>: {},
    <span class="hljs-attr">firstPlayer</span>: <span class="hljs-number">0</span>,
    <span class="hljs-attr">turn</span>: <span class="hljs-number">0</span>,
    <span class="hljs-attr">boxes</span>: [<span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>]
  },

  created () {
    <span class="hljs-keyword">let</span> url = <span class="hljs-keyword">new</span> URL(<span class="hljs-built_in">window</span>.location.href);
    <span class="hljs-keyword">let</span> name = url.searchParams.get(<span class="hljs-string">"username"</span>);

    <span class="hljs-keyword">if</span> (name) {
      <span class="hljs-keyword">this</span>.username = name
      <span class="hljs-keyword">this</span>.subscribe();
      <span class="hljs-keyword">this</span>.listeners();
    } <span class="hljs-keyword">else</span> {
      <span class="hljs-keyword">this</span>.username = <span class="hljs-keyword">this</span>.generateRandomName();
      location.assign(<span class="hljs-string">"/play?username="</span> + <span class="hljs-keyword">this</span>.username);
    }
  },

  <span class="hljs-attr">methods</span>: {
    <span class="hljs-comment">// We will add methods here</span>
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 将`PUSHER_APP_*`键替换为推动器仪表板上的键。

在上面，我们创建了一个新的 Vue 实例，并以`#app`选择器为目标。我们在`data`对象中定义了所有的默认值，然后在创建 Vue 组件时自动调用的`create()`函数中，我们检查一个用户，如果提供了用户名，就将该用户分配给用户名。

我们还调用了`subscribe`和`listeners`方法。让我们在`methods`对象中定义这些。在`methods`对象内部，粘贴以下函数:

```
<span class="hljs-comment">// [...]</span>

subscribe: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
  <span class="hljs-keyword">let</span> channel = <span class="hljs-keyword">this</span>.pusher.subscribe(<span class="hljs-string">'presence-channel'</span>);
  <span class="hljs-keyword">this</span>.myChannel = <span class="hljs-keyword">this</span>.pusher.subscribe(<span class="hljs-string">'private-'</span> + <span class="hljs-keyword">this</span>.username)

  channel.bind(<span class="hljs-string">'pusher:subscription_succeeded'</span>, (player) => {
    <span class="hljs-keyword">this</span>.players = player.count - <span class="hljs-number">1</span>
    player.each(<span class="hljs-function">(<span class="hljs-params">player</span>) =></span> {
      <span class="hljs-keyword">if</span> (player.id != <span class="hljs-keyword">this</span>.username)
        <span class="hljs-keyword">this</span>.connectedPlayers.push(player.id)
    });
  });

  channel.bind(<span class="hljs-string">'pusher:member_added'</span>, (player) => {
    <span class="hljs-keyword">this</span>.players++;
    <span class="hljs-keyword">this</span>.connectedPlayers.push(player.id)
  });

  channel.bind(<span class="hljs-string">'pusher:member_removed'</span>, (player) => {
    <span class="hljs-keyword">this</span>.players--;
    <span class="hljs-keyword">var</span> index = <span class="hljs-keyword">this</span>.connectedPlayers.indexOf(player.id);
    <span class="hljs-keyword">if</span> (index > <span class="hljs-number">-1</span>) {
      <span class="hljs-keyword">this</span>.connectedPlayers.splice(index, <span class="hljs-number">1</span>)
    }
  });
},

<span class="hljs-attr">listeners</span>: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
  <span class="hljs-keyword">this</span>.pusher.bind(<span class="hljs-string">'client-'</span> + <span class="hljs-keyword">this</span>.username, (message) => {
    <span class="hljs-keyword">if</span> (confirm(<span class="hljs-string">'Do you want to start a game of Tic Tac Toe with '</span> + message)) {
      <span class="hljs-keyword">this</span>.otherPlayerName = message
      <span class="hljs-keyword">this</span>.otherPlayerChannel = <span class="hljs-keyword">this</span>.pusher.subscribe(<span class="hljs-string">'private-'</span> + <span class="hljs-keyword">this</span>.otherPlayerName)
      <span class="hljs-keyword">this</span>.otherPlayerChannel.bind(<span class="hljs-string">'pusher:subscription_succeeded'</span>, () => {
        <span class="hljs-keyword">this</span>.otherPlayerChannel.trigger(<span class="hljs-string">'client-game-started'</span>, <span class="hljs-keyword">this</span>.username)
      })
      <span class="hljs-keyword">this</span>.startGame(message)
    } <span class="hljs-keyword">else</span> {
      <span class="hljs-keyword">this</span>.otherPlayerChannel = <span class="hljs-keyword">this</span>.pusher.subscribe(<span class="hljs-string">'private-'</span> + message)
      <span class="hljs-keyword">this</span>.otherPlayerChannel.bind(<span class="hljs-string">'pusher:subscription_succeeded'</span>, () => {
        <span class="hljs-keyword">this</span>.otherPlayerChannel.trigger(<span class="hljs-string">'client-game-declined'</span>, <span class="hljs-string">""</span>)
      })
      <span class="hljs-keyword">this</span>.gameDeclined()
    }
  }),

  <span class="hljs-keyword">this</span>.myChannel.bind(<span class="hljs-string">'client-game-started'</span>, (message) => {
    <span class="hljs-keyword">this</span>.status = <span class="hljs-string">"Game started with "</span> + message
    <span class="hljs-keyword">this</span>.$refs.gameboard.classList.remove(<span class="hljs-string">'invisible'</span>);
    <span class="hljs-keyword">this</span>.firstPlayer = <span class="hljs-number">1</span>;
    <span class="hljs-keyword">this</span>.turn = <span class="hljs-number">1</span>;
  })

  <span class="hljs-keyword">this</span>.myChannel.bind(<span class="hljs-string">'client-game-declined'</span>, () => {
    <span class="hljs-keyword">this</span>.status = <span class="hljs-string">"Game declined"</span>
  })

  <span class="hljs-keyword">this</span>.myChannel.bind(<span class="hljs-string">'client-new-move'</span>, (position) => {
    <span class="hljs-keyword">this</span>.$refs[position].innerText = <span class="hljs-keyword">this</span>.firstPlayer ? <span class="hljs-string">'O'</span> : <span class="hljs-string">'X'</span>
  })

  <span class="hljs-keyword">this</span>.myChannel.bind(<span class="hljs-string">'client-your-turn'</span>, () => {
    <span class="hljs-keyword">this</span>.turn = <span class="hljs-number">1</span>;
  })

  <span class="hljs-keyword">this</span>.myChannel.bind(<span class="hljs-string">'client-box-update'</span>, (update) => {
    <span class="hljs-keyword">this</span>.boxes = update;
  })

  <span class="hljs-keyword">this</span>.myChannel.bind(<span class="hljs-string">'client-you-lost'</span>, () => {
    <span class="hljs-keyword">this</span>.gameLost();
  })
},

<span class="hljs-comment">// [...]</span> 
```

Enter fullscreen mode Exit fullscreen mode

在`subscribe`方法中，我们订阅我们的推送器存在通道，然后为当前用户订阅私有通道。在`listeners`方法中，我们注册了所有事件的监听器，这些事件将在我们订阅的私有通道上被触发。

接下来，我们将在我们的方法类中添加其他帮助方法。在 methods 类中，将以下函数添加到底部的`listeners`方法之后:

```
<span class="hljs-comment">// Generates a random string we use as a name for a guest user</span>
generateRandomName: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
  <span class="hljs-keyword">let</span> text = <span class="hljs-string">''</span>;
  <span class="hljs-keyword">let</span> possible = <span class="hljs-string">'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'</span>;
  <span class="hljs-keyword">for</span> (<span class="hljs-keyword">var</span> i = <span class="hljs-number">0</span>; i < <span class="hljs-number">6</span>; i++) {
    text += possible.charAt(<span class="hljs-built_in">Math</span>.floor(<span class="hljs-built_in">Math</span>.random() * possible.length));
  }
  <span class="hljs-keyword">return</span> text;
},

<span class="hljs-comment">// Lets you choose a player to play as.</span>
choosePlayer: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">e</span>) </span>{
  <span class="hljs-keyword">this</span>.otherPlayerName = e.target.innerText
  <span class="hljs-keyword">this</span>.otherPlayerChannel = <span class="hljs-keyword">this</span>.pusher.subscribe(<span class="hljs-string">'private-'</span> + <span class="hljs-keyword">this</span>.otherPlayerName)
  <span class="hljs-keyword">this</span>.otherPlayerChannel.bind(<span class="hljs-string">'pusher:subscription_succeeded'</span>, () => {
    <span class="hljs-keyword">this</span>.otherPlayerChannel.trigger(<span class="hljs-string">'client-'</span> + <span class="hljs-keyword">this</span>.otherPlayerName, <span class="hljs-keyword">this</span>.username)
  });
},

<span class="hljs-comment">// Begins the game</span>
startGame: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">name</span>) </span>{
  <span class="hljs-keyword">this</span>.status = <span class="hljs-string">"Game started with "</span> + name
  <span class="hljs-keyword">this</span>.$refs.gameboard.classList.remove(<span class="hljs-string">'invisible'</span>);
},

<span class="hljs-comment">// User declined to play</span>
gameDeclined: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
  <span class="hljs-keyword">this</span>.status = <span class="hljs-string">"Game declined"</span>
},

<span class="hljs-comment">// Game has ended with current user winning</span>
gameWon: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
  <span class="hljs-keyword">this</span>.status = <span class="hljs-string">"You WON!"</span>
  <span class="hljs-keyword">this</span>.$refs.gameboard.classList.add(<span class="hljs-string">'invisible'</span>);
  <span class="hljs-keyword">this</span>.restartGame()
},

<span class="hljs-comment">// Game has ended with current user losing</span>
gameLost: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
  <span class="hljs-keyword">this</span>.turn = <span class="hljs-number">1</span>;
  <span class="hljs-keyword">this</span>.boxes = [<span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>]
  <span class="hljs-keyword">this</span>.status = <span class="hljs-string">"You LOST!"</span>
  <span class="hljs-keyword">this</span>.$refs.gameboard.classList.add(<span class="hljs-string">'invisible'</span>);
  <span class="hljs-keyword">this</span>.restartGame()
},

<span class="hljs-comment">// Restarts a game</span>
restartGame: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
  <span class="hljs-keyword">for</span> (i = <span class="hljs-number">1</span>; i < <span class="hljs-number">10</span>; i++) {
    <span class="hljs-keyword">this</span>.$refs[i].innerText = <span class="hljs-string">""</span>
  }
  <span class="hljs-keyword">this</span>.$refs.gameboard.classList.remove(<span class="hljs-string">'invisible'</span>);
},

<span class="hljs-comment">// Checks tiles to see if the tiles passed are a match</span>
compare: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
  <span class="hljs-keyword">for</span> (<span class="hljs-keyword">var</span> i = <span class="hljs-number">1</span>; i < <span class="hljs-built_in">arguments</span>.length; i++) {
    <span class="hljs-keyword">if</span> (<span class="hljs-built_in">arguments</span>[i] === <span class="hljs-number">0</span> || <span class="hljs-built_in">arguments</span>[i] !== <span class="hljs-built_in">arguments</span>[i - <span class="hljs-number">1</span>]) {
      <span class="hljs-keyword">return</span> <span class="hljs-literal">false</span>
    }
  }

  <span class="hljs-keyword">return</span> <span class="hljs-literal">true</span>;
},

<span class="hljs-comment">// Checks the tiles and returns true if theres a winning play</span>
theresAMatch: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params"></span>) </span>{
  <span class="hljs-keyword">return</span> <span class="hljs-keyword">this</span>.compare(<span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">0</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">1</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">2</span>]) ||
    <span class="hljs-keyword">this</span>.compare(<span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">3</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">4</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">5</span>]) ||
    <span class="hljs-keyword">this</span>.compare(<span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">6</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">7</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">8</span>]) ||
    <span class="hljs-keyword">this</span>.compare(<span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">0</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">3</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">6</span>]) ||
    <span class="hljs-keyword">this</span>.compare(<span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">1</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">4</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">7</span>]) ||
    <span class="hljs-keyword">this</span>.compare(<span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">2</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">5</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">8</span>]) ||
    <span class="hljs-keyword">this</span>.compare(<span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">2</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">4</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">6</span>]) ||
    <span class="hljs-keyword">this</span>.compare(<span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">0</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">4</span>], <span class="hljs-keyword">this</span>.boxes[<span class="hljs-number">8</span>])
},

<span class="hljs-comment">// Checks to see if the play was a winning play</span>
playerAction: <span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">e</span>) </span>{
  <span class="hljs-keyword">let</span> index = e.target.dataset.id - <span class="hljs-number">1</span>
  <span class="hljs-keyword">let</span> tile = <span class="hljs-keyword">this</span>.firstPlayer ? <span class="hljs-string">'X'</span> : <span class="hljs-string">'O'</span>

  <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.turn && <span class="hljs-keyword">this</span>.boxes[index] == <span class="hljs-number">0</span>) {
    <span class="hljs-keyword">this</span>.turn = <span class="hljs-number">0</span>
    <span class="hljs-keyword">this</span>.boxes[index] = tile
    e.target.innerText = tile

    <span class="hljs-keyword">this</span>.otherPlayerChannel.trigger(<span class="hljs-string">'client-your-turn'</span>, <span class="hljs-string">""</span>)
    <span class="hljs-keyword">this</span>.otherPlayerChannel.trigger(<span class="hljs-string">'client-box-update'</span>, <span class="hljs-keyword">this</span>.boxes)
    <span class="hljs-keyword">this</span>.otherPlayerChannel.trigger(<span class="hljs-string">'client-new-move'</span>, e.target.dataset.id)

    <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.theresAMatch()) {
      <span class="hljs-keyword">this</span>.gameWon()
      <span class="hljs-keyword">this</span>.boxes = [<span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>]
      <span class="hljs-keyword">this</span>.otherPlayerChannel.trigger(<span class="hljs-string">'client-you-lost'</span>, <span class="hljs-string">''</span>)
    }
  }
}, 
```

Enter fullscreen mode Exit fullscreen mode

上面，我们添加了几个游戏正常运行所需要的辅助方法，在每个方法之前，我们都添加了一个注释来显示这个方法的作用。

现在让我们测试一下这个游戏。

## 测试游戏

我们可以通过运行以下命令来测试游戏:

```
$ flask run 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们访问 [localhost:5000](http://localhost:5000) ，我们应该会看到连接页面并测试游戏:

[![](../Images/8ca1e1d194a0427324c093d10ce91d2f.png)T2】](//images.ctfassets.net/1es3ne0caaid/1RGRfjXfgQgYWYy2kqImOs/0c1db39f9371dbb773638ffe57bfd385/python-vue-game-demo.gif)

## 结论

在本教程中，我们学习了如何利用 Pusher SDK 创建一个由 Python 后端服务器支持的在线多人游戏。

本教程的源代码可以在 [GitHub](https://github.com/neoighodaro/python-pusher-multiplayer-game) 上找到

这篇文章最初出现在 [Pusher 博客](https://pusher.com/tutorials/game-python-vue)上。