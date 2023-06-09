# Elixir API 和 Elm SPA -第 3 部分

> 原文：<https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-3-3n0h>

# 第 3 部分:Elm 应用程序创建和路由设置

现在我们将创建 Elm 应用程序。我们将使用神奇的 [create-elm-app](https://github.com/halfzebra/create-elm-app) ，因为它允许我们使用 webpack 和热模块替换创建一个生产就绪的 elm 项目。

## 系列

1.  [第 1 部分- Elixir 应用程序创建](https://dev.to/miguelcoba/elixir-api-and-elm-spa-4hpf)
2.  [第 2 部分-增加监护人认证](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-2-1pca)
3.  第 3 部分- Elm 应用程序创建和路由设置
4.  [第 4 部分:添加登录和注册页面](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-4-4il0)
5.  [第 5 部分:将会话数据保存到本地存储器](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-5-4j1k)

## 安装 create-elm-app

```
npm install -g create-elm-app 
```

Enter fullscreen mode Exit fullscreen mode

## 创建并启动 app

```
create-elm-app toltec-web
cd toltec-web
elm-app start 
```

Enter fullscreen mode Exit fullscreen mode

您应该在浏览器选项卡中看到 Elm 徽标。让我们现在来构建我们的应用程序。

## 设置连接

对于这个项目，我将使用[超光速粒子](http://tachyons.io/)项目来创建接口。所以让我们把它添加到 index.html
的*头*部分

```
<!-- index.html -->

<link rel="stylesheet" href="https://unpkg.com/tachyons@4.9.1/css/tachyons.min.css" /> 
```

Enter fullscreen mode Exit fullscreen mode

## 设置路由

这个应用程序完全基于理查德费尔德曼优秀的[榆树温泉的例子](https://github.com/halfzebra/elm-spa-example)。
这款应用共享了许多底层基础设施，但删除了一些我们的应用不需要的高级功能。它还使用不同的目录结构，该目录结构是垂直组织的，而不是水平分布在数据/目录、请求/目录、页面/目录和视图/目录中。您会发现，路由、模型形状、消息和编码器/解码器非常相似，如果不是完全相同的话。感谢理查德，所有的辛勤工作:)

在添加 Elm 代码之前，我们需要添加一些依赖项，我们将使用它们来设置路由和导航。

```
elm-app install elm-lang/navigation
elm-app install evancz/url-parser 
```

Enter fullscreen mode Exit fullscreen mode

对于这一部分，我们只有几条路线:

*   首页:#/
*   登录:#/登录
*   注销:#/注销
*   注册:#/注册

让我们在 src/目录
中创建一个名为 Route.elm 的新文件

```
-- src/Route.elm

module Route exposing (Route(..), fromLocation, href, modifyUrl)

import Html exposing (Attribute)
import Html.Attributes as Attr
import Navigation exposing (Location)
import UrlParser as Url exposing ((</>), Parser, oneOf, parseHash, s, string)

type Route
    = Home
    | Root
    | Login
    | Logout
    | Register

route : Parser (Route -> a) a
route =
    oneOf
        [ Url.map Home (s "")
        , Url.map Login (s "login")
        , Url.map Logout (s "logout")
        , Url.map Register (s "register")
        ]

routeToString : Route -> String
routeToString page =
    let
        pieces =
            case page of
                Home ->
                    []

                Root ->
                    []

                Login ->
                    [ "login" ]

                Logout ->
                    [ "logout" ]

                Register ->
                    [ "register" ]
    in
        "#/" ++ String.join "/" pieces

href : Route -> Attribute msg
href route =
    Attr.href (routeToString route)

modifyUrl : Route -> Cmd msg
modifyUrl =
    routeToString >> Navigation.modifyUrl

fromLocation : Location -> Maybe Route
fromLocation location =
    if String.isEmpty location.hash then
        Just Root
    else
        parseHash route location 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个包含可能路线的路线类型和一个将被传递给 *parseHash* 函数的*路线*函数，以帮助它确定哪个路线对应于浏览器 url 栏中的当前位置。
*from location*函数负责浏览器地址栏和我们 app 路线之间的映射。

接下来，我们在应用程序中添加一条消息来设置路线。创建一个 Messages.elm 文件:

```
-- src/Messages.elm

module Messages exposing (Msg(..))

import Route exposing (Route)

type Msg
    = SetRoute (Maybe Route) 
```

Enter fullscreen mode Exit fullscreen mode

目前，我们只有这条消息来在我们的应用程序中设置新的路线。

在继续之前，添加这个从 Richard 的应用程序复制的 util . elm:

```
-- src/Util.elm

module Util exposing ((=>), pair)

(=>) : a -> b -> ( a, b )
(=>) =
    (,)

{-| infixl 0 means the (=>) operator has the same precedence as (<|) and (|>),
meaning you can use it at the end of a pipeline and have the precedence work out.
-}
infixl 0 =>

{-| Useful when building up a Cmd via a pipeline, and then pairing it with
a model at the end.
session.user
|> User.Request.foo
|> Task.attempt Foo
|> pair { model | something = blah }
-}
pair : a -> b -> ( a, b )
pair first second =
    first => second 
```

Enter fullscreen mode Exit fullscreen mode

我们最初的 Model.elm 是这样的:

```
-- src/Model.elm

module Model exposing (Model, initialModel, Page(..), PageState(..), getPage)

import Json.Decode as Decode exposing (Value)

type Page
    = Blank
    | NotFound
    | Home
    | Login
    | Register

type PageState
    = Loaded Page
    | TransitioningFrom Page

type alias Model =
    { pageState : PageState
    }

initialModel : Value -> Model
initialModel val =
    { pageState = Loaded Blank
    }

getPage : PageState -> Page
getPage pageState =
    case pageState of
        Loaded page ->
            page

        TransitioningFrom page ->
            page 
```

Enter fullscreen mode Exit fullscreen mode

最初，该应用程序将管理一个很小的页面集，如你所见。在 Richards 应用程序之后，我们将拥有一个完全加载的页面，或者我们将处于从当前页面到新页面的过渡状态。因此，该模型保存当前页面状态，即最初是一个空白页。

最初的 app 正在慢慢成型。现在让我们添加 Update.elm 文件:

```
-- src/Update.elm

module Update exposing (update, init)

import Json.Decode as Decode exposing (Value)
import Model exposing (Model, initialModel, Page(..), PageState(..), getPage)
import Messages exposing (Msg(..))
import Navigation exposing (Location)
import Route exposing (Route)
import Util exposing ((=>))

init : Value -> Location -> ( Model, Cmd Msg )
init val location =
    updateRoute (Route.fromLocation location) (initialModel val)

updateRoute : Maybe Route -> Model -> ( Model, Cmd Msg )
updateRoute maybeRoute model =
    case maybeRoute of
        Nothing ->
            { model | pageState = Loaded NotFound } => Cmd.none

        Just Route.Home ->
            { model | pageState = Loaded Home } => Cmd.none

        Just Route.Root ->
            model => Route.modifyUrl Route.Home

        Just Route.Login ->
            { model | pageState = Loaded Login } => Cmd.none

        Just Route.Logout ->
            model => Cmd.none

        Just Route.Register ->
            { model | pageState = Loaded Register } => Cmd.none

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    updatePage (getPage model.pageState) msg model

updatePage : Page -> Msg -> Model -> ( Model, Cmd Msg )
updatePage page msg model =
    case ( msg, page ) of
        ( SetRoute route, _ ) ->
            updateRoute route model 
```

Enter fullscreen mode Exit fullscreen mode

这个模块公开了两个函数，第一个是 *init* ，它接受一个初始值和一个位置，并返回第一个元组(Model，Cmd Msg ),它将引导我们的应用程序状态。如您所见，当应用程序启动时，状态将只取决于两件事值(我们将在后面看到)和在 url 中的位置。

另一个公开的函数是*更新*函数。这非常简单，因为它将真正的工作委托给了*更新页面*。这最后一个将是应用程序逻辑驻留的地方。现在它做不了太多，它只知道一条消息，SetRoute 消息，当得到一条消息时，它更新应用程序路由。

最后， *updateRoute* 是声明特定于路由更改的逻辑的地方。根据所传递的路径，它会在加载时建立一个特定的页面。现在，注销路由什么也不做。

View.elm 文件如下:

```
-- src/View.elm

module View exposing (..)

import Html exposing (..)
import Model exposing (Model, Page(..), PageState(..))
import Messages exposing (Msg(..))
import Page.Page as Page exposing (ActivePage)
import Page.Home as Home
import Page.NotFound as NotFound
import Session.Login as Login
import Session.Register as Register

view : Model -> Html Msg
view model =
    case model.pageState of
        Loaded page ->
            viewPage True page

        TransitioningFrom page ->
            viewPage False page

viewPage : Bool -> Page -> Html Msg
viewPage isLoading page =
    let
        frame =
            Page.frame isLoading
    in
        case page of
            NotFound ->
                NotFound.view
                    |> frame Page.Other

            Blank ->
                Html.text "Loading Maya!"

            Home ->
                Home.view
                    |> frame Page.Home

            Login ->
                Login.view
                    |> frame Page.Login

            Register ->
                Register.view
                    |> frame Page.Register 
```

Enter fullscreen mode Exit fullscreen mode

这很简单，但是使用了很多新的模块。本质上，它所做的是从模型中检索页面，然后呈现适当的视图。
*viewPage*函数接收页面，然后呈现几个视图中选择的一个。

视图是这样定义的:

```
 -- src/Page/NotFound.elm

module Page.NotFound exposing (view)

import Html exposing (Html, h1, div, text)

view : Html msg
view =
    div []
        [ h1 [] [ text "The page you requested was not found!" ] ]

-- src/Page/Home.elm

module Page.Home exposing (view)

import Html exposing (..)

view : Html msg
view =
    div []
        [ h1 [] [ text "Toltec" ]
        , p [] [ text "Welcome to Toltec!" ]
        ]

-- src/Session/Login.elm

module Session.Login exposing (view)

import Html exposing (..)

view : Html msg
view =
    div []
        [ h1 [] [ text "Login page" ] ]

-- src/Session/Register.elm

module Session.Register exposing (view)

import Html exposing (..)

view : Html msg
view =
    div []
        [ h1 [] [ text "Register page" ] ] 
```

Enter fullscreen mode Exit fullscreen mode

它们非常简单，只呈现一些文本，以便我们可以检查路由是否工作。注意，我们创建了两个新目录:Session/和 Page/。

缺少的部分是 Page.frame 函数，它获取内容并在其周围放置一个 above。

```
-- src/Page/Page.elm

module Page.Page exposing (ActivePage(..), frame)

import Html exposing (..)
import Html.Attributes exposing (..)
import Route exposing (Route)

type ActivePage
    = Other
    | Home
    | Login
    | Register

frame : Bool -> ActivePage -> Html msg -> Html msg
frame isLoading activePage content =
    div []
        [ viewHeader activePage isLoading
        , content
        ]

viewHeader : ActivePage -> Bool -> Html msg
viewHeader activePage isLoading =
    nav [ class "dt w-100 border-box pa3 ph5-ns" ]
        [ a [ class "dtc v-mid mid-gray link dim w-25", Route.href Route.Home, title "Home" ]
            [ text "Maya" ]
        , div [ class "dtc v-mid w-75 tr" ] <|
            [ navbarLink activePage Route.Home [ text "Home" ]
            , navbarLink activePage Route.Login [ text "Login" ]
            , navbarLink activePage Route.Register [ text "Register" ]
            ]
        ]

navbarLink : ActivePage -> Route -> List (Html msg) -> Html msg
navbarLink activePage route linkContent =
    let
        active =
            case isActive activePage route of
                True ->
                    "black"

                False ->
                    "gray"
    in
        a [ Route.href route, class "link hover-black f6 f5-ns dib mr3 mr4-ns", class active ] linkContent

isActive : ActivePage -> Route -> Bool
isActive activePage route =
    case ( activePage, route ) of
        ( Home, Route.Home ) ->
            True

        ( Login, Route.Login ) ->
            True

        ( Register, Route.Register ) ->
            True

        _ ->
            False 
```

Enter fullscreen mode Exit fullscreen mode

正如 Richard 解释的那样， *frame* 函数为我们传递给它的内容添加了一个头。它还负责检测我们显示的页面是否与导航栏上的任何链接相匹配。暂时我们忽略 isLoading 参数。

有了这些，我们就可以编写主函数了。将 Main.elm 更改为:

```
-- src/Main.elm

module Main exposing (main)

import Json.Decode as Decode exposing (Value)
import Navigation
import Model exposing (Model)
import Messages exposing (Msg(..))
import Route exposing (Route)
import Subscriptions exposing (subscriptions)
import Update exposing (update, init)
import View exposing (view)

main : Program Value Model Msg
main =
    Navigation.programWithFlags (Route.fromLocation >> SetRoute)
        { init = init
        , view = view
        , update = update
        , subscriptions = subscriptions
        } 
```

Enter fullscreen mode Exit fullscreen mode

超级简单。它使用[navigation . programwithflags](http://package.elm-lang.org/packages/elm-lang/navigation/latest/Navigation#programWithFlags)来启动我们的应用程序。
该函数将一个(Location - > msg)函数作为第一个参数，该函数将一个位置转换成一条消息。在我们的例子中，消息是 SetRoute。每次 url 改变时，*navigation . programwithflags*函数将使用此函数获取消息，并调用 *update* 函数向其传递此消息。本质上，它观察 URL 的变化，并通过调用 update 函数触发 Elm 循环。
当然，此外，当应用程序启动时，它可以从 JavaScript land 接收一个初始值，但我们现在不使用它。

在 Subscriptions.elm 文件:
中定义了*订阅*函数

```
-- src/Subscriptions.elm

module Subscriptions exposing (subscriptions)

import Model exposing (Model, Page(..), PageState(..), getPage)
import Messages exposing (Msg(..))

subscriptions : Model -> Sub Msg
subscriptions model =
    Sub.batch
        [ pageSubscriptions (getPage model.pageState)
        ]

pageSubscriptions : Page -> Sub Msg
pageSubscriptions page =
    case page of
        Blank ->
            Sub.none

        NotFound ->
            Sub.none

        Home ->
            Sub.none

        Login ->
            Sub.none

        Register ->
            Sub.none 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经把所有的部分都准备好了，我们可以测试我们的应用了。 *elm-app start* 命令应该已经热重新加载了所有这些更改，应用应该已经准备好了。检查终端，确保输出中没有任何错误。应该是这样的:

```
Compiled successfully!

You can now view toltec-web in the browser.  Local:            http://localhost:3000/
  On Your Network:  http://192.168.1.108:3000/
Note that the development build is not optimized.
To create a production build, use elm-app build. 
```

Enter fullscreen mode Exit fullscreen mode

如果您检查您的浏览器，您应该会看到类似这样的内容:

[![Toltec Home page](img/bcc8a61b41692885249d8fdd9d5979f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dvcP5hWM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ic50eacmqrqd4gcpsel.png)

如果你点击导航栏中的链接，你会看到它们根据浏览器中对应于 url 的页面正确地切换视图。

你可以在分支 part-03 的 repo [这里的](https://github.com/miguelcoba/toltec-web)中找到带有测试的源代码。

很好，我们现在就在这里结束吧。我们已经创建了 Elm 应用程序并配置了路由。

在第 4 部分中，我们将添加登录和注册页面，并将它们连接到我们的后端 Elixir API。