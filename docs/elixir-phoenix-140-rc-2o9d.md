# Elixir: Phoenix 1.4.0-rc 作为发行候选项公开

> 原文：<https://dev.to/gumi/elixir-phoenix-140-rc-2o9d>

Phoenix 1.4 的第一个版本候选项已发布。 本文是 Elixir Forum 的投稿“[Phoenix 1.4.0-rc.0 released！ 根据](https://elixirforum.com/t/phoenix-1-4-0-rc-0-released/17182)，介绍其内容。 Phoenix 1.4 的主要新功能包括:

*   支持 HTTP2
*   生成本地 SSL 证书
*   缩短编译时间
*   新错误页面

此外，结构将得到改善，可扩展性将得到提高。 为了进行路由和 DSL 测试，还引入了与新的 JavaScript API `Presence`和 Elixir 格式化程序的集成。

# hex phx_new 档案库

`mix phx.new`归档文件可以用 hex 安装。 会对更简单的版本管理有帮助吧。 要创建新的归档文件，请输入`mix`命令:

```
$  mix archive.uninstall phx_new
$  mix archive.install hex phx_new 1.4.0-rc.1 
```

Enter fullscreen mode Exit fullscreen mode

新生成器使用[Milligram](https://milligram.io/) 使 Bootstrap 可以生成无类标记。 结果，默认外观很好，而且根据各自的 CSS 要求定制生成的标注也很容易。

另外，phoenix APP 应用程序仍将一如既往地在 Elixir 1.4 中运行。 但是，新的`phx.new`归档需要 Elixir 1.5 以上。

# HTTP2 対応

随着 Cowboy 2 的发行，Phoenix 1.4 只需在`mix.exs`中添加以下一行即可支持 H2。

```
defp deps do
  [

    {:cowboy, "~> 2.5"}

  ]
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，Phoenix 将在 Cowboy 2 下运行。

# 本地 SSL 开发

在大多数浏览器中，HTTP2 请求需要通过 SSL 进行连接。 否则，回退到 HTTP 1.1 请求。 为了本地 SSL 开发，Phoenix 新具备了`phx.gen.cert`任务。 在开发中测试 HTTPS 时，可以生成自我证书的功能。

# 缩短编译时间

通过在 Plug 和编译时进行修改，提高了开发的编译速度。 详情请参阅“[what ' s new in phoenix development-February 2018](https://dockyard.com/blog/2018/02/12/what-s-new-in-phoenix-development-february-2018)”的“Faster development compilation”一节。

# 新开发用第 404 页

开发时的第 404 页列出了从路由器可用的路由(图 001 )。

#### [t1■新的第 404 页](#%E5%9B%B3001%E2%96%A0%E6%96%B0%E3%81%97%E3%81%84404%E3%83%9A%E3%83%BC%E3%82%B8)

[![新しい404ページ](../Images/df16609c5d9d8edd95b0da17d74e9cc7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SBy6t-LM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://elixirforum.com/uploads/default/original/2X/8/8b1ab227fd84ec401eeb69bf7570301da12c992d.jpeg)

# 用户套接字的连接信息

一直以来，人们要求的呼声很高的是，使用 Phoenix 频道时，要参考基本的传输信息。 Phoenix 1.4 配备了用户套接字的回调`connect/3`。 生成的连接信息包括 web 套接字和长轮询传输 HTTP 请求中的对等 IP 地址、主机信息和 X-Header。

# 过时的传输宏

随着通道的翻新，不再推荐传输宏。 因为传输信息是通过从端点进行套接字调用直接获得的。 可以简单改写如下。

```
# app_web/channels/user_socket.ex
- transport :websocket, Phoenix.Transports.WebSocket
- transport :longpoll, Phoenix.Transports.LongPoll, [check_origin: ...]

# app_web/endpoint.ex
- socket "/socket", MyAppWeb.UserSocket
+ socket "/socket", MyAppWeb.UserSocket, 
+   websocket: true # or list of options
+   longpoll: [check_origin: ...] 
```

Enter fullscreen mode Exit fullscreen mode

# 新しいPresence JavaScript API

新采用了向后兼容的`Presence`JavaScript API。 可以解决竞争条件，使用方法也很简单。 到目前为止，已请求客户端对`presence_state`和`presence_diff`事件进行多个通道回调。 然后，从客户端调用的是函数`Presence.syncState`和`Presence.syncDiff`。 那个接口被合并为一个回调`onSync`。 监视信道回调和状态的是`Presence`对象。

```
let presence = new Presence(roomChannel)
presence.onSync(() => {
  console.log("users online:", presence.list((id, {name}) => name))
}) 
```

Enter fullscreen mode Exit fullscreen mode

# webpack

`mix phx.new`生成器使用 web 包而不是 brunch 来生成资源。 开发的方法不变。 每个文件都存储在以下目录中:

*   assets/js: JavaScript
*   资产/css: CSS
*   资产/静态:静态资产

即使不熟悉 JavaScript 工具的人也可以用 webpack 进行同样的开发吧。 寻求优化的人将受益于 webpack，包括精心设计的代码绑定和消除不必要的代码。

# 升级指南

在此版本中，对用户没有明显的影响。 从 1.3.x 升级也很简单。 有关步骤和详细内容，请阅读“[phoenix 1.3.x to 1.4.0 upgrade guides](https://gist.github.com/chrismccord/bb1f8b136f5a9e4abc0bfc07b832257e)”。

“[Programming Phoenix 1.4](https://pragprog.com/book/phoenix14/programming-phoenix-1-4) ”现在正在测试版中发售。 详细的改善和修正项目请参照 Elixir Forum「 [Phoenix 1.4.0-rc.0 released！ 请参阅](https://elixirforum.com/t/phoenix-1-4-0-rc-0-released/17182)。