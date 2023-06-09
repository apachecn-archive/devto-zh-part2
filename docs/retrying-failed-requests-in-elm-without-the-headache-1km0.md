# 在 Elm 中重试失败的请求...没有头痛

> 原文：<https://dev.to/1hko/retrying-failed-requests-in-elm-without-the-headache-1km0>

我需要一种通用的方法来重试失败的请求。我不想给我的模型添加额外的状态，也不想因为必须为每个请求的代码路径处理额外的情况而搞乱我的`update`函数。

我找到了 [panosoft/elm-cmd-retry](https://github.com/panosoft/elm-cmd-retry) ，但是它依赖于本地代码，不满足上面设置的任何一个要求。

## 一厢情愿

使用起来应该很直观—

```
decoder
    |> Http.get url
    |> Retry.send DataReceived 
```

Enter fullscreen mode Exit fullscreen mode

它应该允许我们重试配置—

```
decoder
    |> Http.get url
    |> Retry.sendWith { retries = 3, interval = 10 * Time.second } DataReceived 
```

Enter fullscreen mode Exit fullscreen mode

## 实现

下面是我在`Retry`模块的最佳尝试。由于在[榆树论坛](https://discourse.elm-lang.org/t/retrying-failed-requests-without-the-headache/1445)上收到了一些评论，我调整了 API。修改后的实现也使得使用`retry`或`retryWith`重试任何任务成为可能。它按照我的程序的意图编译和运行。

一目了然—

```
default : Config
retry : Task x a -> Task x a
retryWith : Config -> Task x a -> Task x a
send : (Result Http.Error a -> msg) -> Http.Request a -> Cmd msg
sendWith : Config -> (Result Http.Error a -> msg) -> Http.Request a -> Cmd msg 
```

Enter fullscreen mode Exit fullscreen mode

完成实施—

```
module Retry exposing (Config, default, retry, retryWith, send, sendWith)

import Http
import Process
import Task exposing (Task)
import Time exposing (Time)

type alias Config =
    { retries : Int
    , interval : Time
    }

default : Config
default =
    { retries = 5
    , interval = 1 * Time.second
    }

retry : Task x a -> Task x a
retry =
    retryWith default

retryWith : Config -> Task x a -> Task x a
retryWith config task =
    task |> Task.onError (onError config task)

send : (Result Http.Error a -> msg) -> Http.Request a -> Cmd msg
send =
    sendWith default

sendWith : Config -> (Result Http.Error a -> msg) -> Http.Request a -> Cmd msg
sendWith config resultToMessage req =
    let
        task =
            Http.toTask req
    in
        task
            |> Task.onError (onError config task)
            |> Task.attempt resultToMessage

onError : Config -> Task x a -> x -> Task x a
onError config task error =
    if config.retries == 0 then
        let
            _ =
                Debug.log "failed retrying" error
        in
            Task.fail error
    else
        let
            _ =
                Debug.log ("retrying " ++ (toString config.retries)) error

            next =
                task
                    |> Task.onError (onError { config | retries = config.retries - 1 } task)
        in
            Process.sleep config.interval
                |> Task.andThen (always next) 
```

Enter fullscreen mode Exit fullscreen mode

#### 如何使用

如下例所示，我们达到了目标。我们的模型不需要跟踪额外的状态，并且很容易将任何单次尝试请求转换为重试请求。

```
module SomeModule exposing (..)

import Retry
import User exposing (User)
...

type alias Model =
    { users : List User }

type Msg
    = UsersReceived (Result Http.Error (List User))

init : ( Model, Cmd Msg )
init =
    ( { users = [] }, getUsers )

getUsers : Cmd Msg
getUsers =
    (Decode.list User.decode)
        |> Http.get "/users.json"
        |> Retry.send UsersReceived

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        UsersReceived (Ok users) ->
            { model | users = users }
                ! []

        UsersReceived (Err e) ->
            let
                _ =
                    Debug.log "request failed" e
            in
                ( model, Cmd.none ) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 反馈

我是 Elm 的新成员，我正在征求社区对这个模块的总体反馈。

*   你能看出我可能会遇到的问题吗？
*   你能找到改进的方法吗？
*   我打破了什么榆树惯例吗？

## 原始 API

最初，我用返回一个`Task`的`retry`和`retryWith`函数编写了`Retry`模块。这需要程序员使用`Task.attempt`来创建必要的`Cmd`。如果可能的话，最好让程序员完全忘记这个实现细节。`Retry.send`和`Retry.sendWith` API 解决了这个问题

```
-- original API leaks Task abstraction
decoder
    |> Http.get url
    |> Retry.retry
    |> Task.attempt DataReceived

-- proposal
decoder
    |> Http.get url
    |> Retry.retry DataReceived

-- proposal with configuration
decoder
    |> Http.get url
    |> Retry.retryWith { ... } DataReceived

-- new API
decoder
    |> Http.get url
    |> Retry.send DataReceived

-- new API with configuration
decoder
    |> Http.get url
    |> Retry.sendWith { retries = 3, interval = 10 * Time.second } DataReceived 
```

Enter fullscreen mode Exit fullscreen mode