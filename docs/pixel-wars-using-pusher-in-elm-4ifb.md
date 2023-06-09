# 像素战争:在 Elm 中使用 Pusher

> 原文：<https://dev.to/avalander/pixel-wars-using-pusher-in-elm-4ifb>

[在 GitHub 上查看项目](https://github.com/Avalander/pixel-wars)

由于我在我的项目中使用 Elm，我需要弄清楚如何在 Elm 中使用 Pusher，因为它没有 Pusher 客户端。幸运的是，Elm 提供了一种非常干净漂亮的与 Javascript 交互的方式。我对它的简单程度感到非常惊讶。

# 在榆树上使用推动器

让我们从代码开始。我是这样做的。

首先，我在一个 Elm 模块中声明了两个端口。

```
port module Pusher exposing (..)

port messages : (Pony -> msg) -> Sub msg

port connect : String -> Cmd msg 
```

Enter fullscreen mode Exit fullscreen mode

*   `messages`将在每次推送订阅收到消息时从 Javascript 调用。别管什么类型的`Pony`，当我在后台更新游戏状态时，它会改变。
*   将从 Elm 调用`connect`来创建推送器连接。Javascript 将监听该消息并打开连接。

让我们看看实现这一点所需的 Javascript 代码。

```
import Elm from 'app/Main.elm'
import Pusher from 'pusher-js'

const mount_node = document.querySelector('#root')
const app = Elm.Main.embed(mount_node)
let pusher

app.ports.connect.subscribe(() => {
    pusher = new Pusher(PUSHER_KEY, {
        encrypted: true,
    })
    pusher.subscribe('ponies')
        .bind('pony-data', app.ports.messages.send)
}) 
```

Enter fullscreen mode Exit fullscreen mode

Elm 中所有以`port`为前缀的函数都在对象`app.ports`中的 Javascript 中公开。因此，当在 Elm 中执行`Pusher.connect`命令时，我可以调用`app.ports.connect.subscribe`在 Javascript 中运行回调。以类似的方式，我可以调用`app.ports.messages.send`来调用 Elm 中的函数`messages`。

要粘合 Elm 中的所有东西，我需要订阅函数`messages`，当我想连接到 pusher 时，在`update`函数中返回命令`connect`。下面是我在我的`Main.elm`里是怎么做到的。

```
import Pusher

type Msg
    = OnPusherMessage Pony
    | OnFetchGame (WebData GameResponse)

update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
    case msg of
        OnPusherMessage pony ->
            ({ model | name = pony.name }, Cmd.none)
        OnFetchGame response ->
            (
                updateModel model response,
                Pusher.connect "connect"
            )

subscriptions : Model -> Sub Msg
subscriptions model =
    Pusher.messages OnPusherMessage 
```

Enter fullscreen mode Exit fullscreen mode

我们走吧。每次在 Javascript 中调用`app.ports.message.send`时，在订阅中添加`Pusher.messages`将触发带有接收消息的`OnPusherMessage`。类似地，当客户端接收到游戏状态时，返回`update`中的`Pusher.connect`将向`app.ports.connect`发送一个事件，以便更新可以开始流动。

# 下一步

下一步是让客户端向服务器发送点击，让服务器更新游戏状态，并将其发送回所有连接的客户端。希望我能在几天内开发出足够实用的东西来部署。