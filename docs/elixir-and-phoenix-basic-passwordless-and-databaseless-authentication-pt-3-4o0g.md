# Elixir 和 Phoenix 基本无密码和无数据库认证(pt。3)

> 原文：<https://dev.to/bigardone/elixir-and-phoenix-basic-passwordless-and-databaseless-authentication-pt-3-4o0g>

这个帖子属于 **Elixir 和 Phoenix basic 无密码无数据库认证**系列。

1.  [项目设置以及存储和验证认证令牌的初始功能](https://dev.to/bigardone/elixir-and-phoenix-basic-passwordless-and-databaseless-authentication-pt-1-3clj)
2.  [发送认证链接邮件和用户套接字连接](https://dev.to/bigardone/elixir-and-phoenix-basic-passwordless-and-databaseless-authentication-pt-2-1haa)
3.  [将 webpack 设置为我们的资产捆绑器和 Elm 单页应用程序](https://dev.to/bigardone/elixir-and-phoenix-basic-passwordless-and-databaseless-authentication-pt-3-4o0g)

[源代码](https://github.com/bigardone/passwordless-auth)

在本系列的[前一部分](https://dev.to/bigardone/elixir-and-phoenix-basic-passwordless-and-databaseless-authentication-pt-2-1haa)中，我们介绍了关于发送认证邮件和使用生成的令牌来认证 **Phoenix** 套接字连接的所有后端逻辑。在这一部分，我们将重点放在前端，构建 **Elm** 单页面应用程序框架，并使用 websockect 认证对未经认证的用户隐藏其中的一些部分。让我们开始吧！

## 前端设置

我在后端使用**仙丹**和**凤凰**，在前端使用**榆树**，已经快三年了，我对结果很满意。我倾向于使用 Phoenix 的默认资产捆绑器， [Brunch](https://brunch.io/) ，但是我最近在使用它的时候遇到了一些问题，尤其是在使用它的 **Elm** 和 **SASS** 插件的时候。因此，对于这个特定的实验，我想测试其他替代方案，用 [webpack](https://webpack.js.org/) 替换 Brunch，用 [Tailwind CSS](https://tailwindcss.com/) 替换 **SASS** ，我开始非常喜欢它，它似乎是我的前端堆栈中第一个替换 **SASS** 的有力候选。回想一下这个系列的第一部分，我们使用- no-brunch 标志创建了这个项目，它没有安装 brunch，这样我们就可以直接添加 **webpack** 。为此，我从 [@_GazD](https://twitter.com/_GazD) 跟随[这篇伟大的文章](http://www.schmitty.me/stack-setup-phoenix-elm-graphql-tailwindcss/)，取我需要的部分并做了一些小的改动来添加 **Elm** 支持。

### 向项目添加 webpack

话虽如此，让我们从向`passwordless_auth_web`应用程序添加一个`package.json`文件开始:

```
//  apps/passwordless_auth_web/assets/package.json  {  "repository":  {},  "license":  "MIT",  "scripts":  {  "deploy":  "webpack --mode production",  "watch":  "webpack --mode development --watch"  },  "dependencies":  {  "phoenix":  "file:../../../deps/phoenix",  "phoenix_html":  "file:../../../deps/phoenix_html"  },  "devDependencies":  {  "autoprefixer":  "^8.6.0",  "babel-core":  "^6.26.0",  "babel-loader":  "^7.1.3",  "babel-preset-env":  "^1.6.1",  "copy-webpack-plugin":  "^4.5.0",  "css-loader":  "^0.28.10",  "elm":  "^0.18.0",  "elm-webpack-loader":  "^4.5.0",  "mini-css-extract-plugin":  "^0.4.0",  "optimize-css-assets-webpack-plugin":  "^4.0.0",  "postcss-loader":  "^2.1.5",  "style-loader":  "^0.21.0",  "tailwindcss":  "^0.5.3",  "uglifyjs-webpack-plugin":  "^1.2.4",  "webpack":  "^4.10.0",  "webpack-cli":  "^3.0.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这些都是我们添加对 **webpack_、_ **Elm** 和 **Tailwind CSS** 的支持所需要的依赖项，尽管要记住在我写完这一部分之后，包的版本可能已经过时了。我们可以继续添加必要的配置文件:

```
$ curl https://raw.githubusercontent.com/phoenixframework/phoenix/master/installer/templates/phx_assets/webpack/webpack.config.js > apps/passwordless_auth_web/assets/webpack.config.js
$ curl https://raw.githubusercontent.com/phoenixframework/phoenix/master/installer/templates/phx_assets/webpack/babelrc > apps/passwordless_auth_web/assets/.babelrc 
```

Enter fullscreen mode Exit fullscreen mode

让我们继续安装所有必要的依赖项:

```
$ cd apps/passwordless_auth_web/assets && npm install 
```

Enter fullscreen mode Exit fullscreen mode

我们希望 **Phoenix** 更新我们在开发时所做的更改，所以让我们为此添加方便的观察者:

```
# apps/passwordless_auth_web/config/dev.exs

use Mix.Config

config :passwordless_auth_web, PasswordlessAuthWeb.Endpoint,
  http: [port: 4000],
  debug_errors: true,
  code_reloader: true,
  check_origin: false,
  watchers: [
    node: [
      "node_modules/webpack/bin/webpack.js",
      "--mode",
      "development",
      "--watch-stdin",
      cd: Path.expand("../assets", __DIR__ )
    ]
  ]

# ... 
```

Enter fullscreen mode Exit fullscreen mode

### 配置顺风 CSS

要生成 Tailwind 的配置文件，我们必须做以下事情:

```
$ cd apps/passwordless_auth_web/assets && ./node_modules/.bin/tailwind init 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们必须使用 **postcss** :
使 **webpack** 处理 **CSS** 和 **Tailwind**

```
// apps/passwordless_auth_web/assets/webpack.config.js

const path = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const CopyWebpackPlugin = require('copy-webpack-plugin');

module.exports = (env, options) => ({
  optimization: {
    minimizer: [
      new UglifyJsPlugin({ cache: true, parallel: true, sourceMap: false }),
      new OptimizeCSSAssetsPlugin({}),
    ],
  },
  entry: './js/app.js',
  output: {
    filename: 'app.js',
    path: path.resolve(__dirname, '../priv/static/js'),
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
        },
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader'],
      }
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({ filename: '../css/app.css' }),
    new CopyWebpackPlugin([{ from: 'static/', to: '../' }]),
  ],
});

// apps/passwordless_auth_web/assets/postcss.config.js

module.exports = {
  plugins: [
    require('tailwindcss')('./tailwind.js'),
    require('autoprefixer')
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要在主 javascript 文件中导入主 **CSS** 文件，并添加所有必要的 **Tailwind** 钩子:

```
// apps/passwordless_auth_web/assets/js/app.js

import css from "../css/app.css";

/* apps/passwordless_auth_web/assets/css/app.css */

@tailwind preflight;
@tailwind components;
@tailwind utilities; 
```

Enter fullscreen mode Exit fullscreen mode

### 添加榆树支持

为了让 **Elm** 与 **webpack** 一起工作，我们需要编辑它的配置文件:

```
// apps/passwordless_auth_web/assets/webpack.config.js

// ...

module.exports = (env, options) => ({
  // ...

  module: {
    rules: [
      // ...
      {
        test: /\.elm$/,
        exclude: ['/elm-stuff/', '/node_modules'],
        loader: 'elm-webpack-loader',
        options: {
          debug: true,
          warn: true,
          cwd: path.resolve(__dirname, 'elm'),
        },
      },
    ],
    noParse: [/.elm$/],
  },
  plugins: [
    new MiniCssExtractPlugin({ filename: '../css/app.css' }),
    new CopyWebpackPlugin([{ from: 'static/', to: '../' }]),
  ],
}); 
```

Enter fullscreen mode Exit fullscreen mode

为了安装所有必要的 **Elm** 包，让我们添加以下文件:

```
//  apps/passwordless_auth_web/assets/elm/elm-package.json  {  "version":  "1.0.0",  "summary":  "helpful summary of your project, less than 80 characters",  "repository":  "https://github.com/user/project.git",  "license":  "BSD3",  "source-directories":  [  "./src",  "./vendor"  ],  "exposed-modules":  [],  "dependencies":  {  "NoRedInk/elm-decode-pipeline":  "3.0.0 <= v < 4.0.0",  "elm-lang/core":  "5.1.1 <= v < 6.0.0",  "elm-lang/html":  "2.0.0 <= v < 3.0.0",  "elm-lang/http":  "1.0.0 <= v < 2.0.0",  "elm-lang/navigation":  "2.1.0 <= v < 3.0.0",  "elm-lang/websocket":  "1.0.2 <= v < 2.0.0",  "evancz/url-parser":  "2.0.1 <= v < 3.0.0"  },  "elm-version":  "0.18.0 <= v < 0.19.0"  } 
```

Enter fullscreen mode Exit fullscreen mode

并运行以下命令:

```
$ cd apps/passwordless_auth_web/assets/elm && elm package install 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以添加一个非常基本的 **Elm** 应用程序来检查一切是否按预期运行:

```
-- apps/passwordless_auth_web/assets/elm/src/Main.elm

module Main exposing (..)

import Html exposing (Html)

type alias Model =
    {}

init : ( Model, Cmd Msg )
init =
    {} ! []

type Msg
    = Noop

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        Noop ->
            model ! []

view : Model -> Html Msg
view model =
    Html.text "Hello from Elm!"

subscriptions : Model -> Sub Msg
subscriptions model =
    Sub.none

main : Program Never Model Msg
main =
    Html.program
        { init = init
        , view = view
        , update = update
        , subscriptions = subscriptions
        } 
```

Enter fullscreen mode Exit fullscreen mode

我们可能还有来自 **Phoenix** 的默认布局模板，所以让我们用下面的内容替换它:

```
<!-- apps/passwordless_auth_web/lib/passwordless_auth_web/templates/layout/app.html.eex -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="author" content="">
    <link rel="stylesheet" href="<%= static_path(@conn, "/css/app.css") %>">
    Passwordless Auth
  </head>

  <body>
    <%= render @view_module, @view_template, assigns %>
    <script src="<%= static_path(@conn, "/js/app.js") %>"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要添加一个容器，我们将在其中呈现 **Elm** 应用程序:

```
<!-- apps/passwordless_auth_web/lib/passwordless_auth_web/templates/page/index.html.eex -->

<div id="elm-main" class="flex items-center justify-center h-screen">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

最后，创建 **Elm** 应用程序，并在它的容器
中呈现它

```
// apps/passwordless_auth_web/assets/js/app.js

import css from '../css/app.css';
import Elm from '../elm/src/Main.elm';

const elmDiv = document.getElementById('elm-main');

if (elmDiv) {
  const app = Elm.Main.embed(elmDiv);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经准备好启动 Phoenix 的服务器，访问 [http://localhost:4000](http://localhost:4000) 并观看我们的基本 **Elm** 应用程序正确渲染，耶！

[![Basic application](img/056175cff8f48947b27a7c393fec3b80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FGxciL7O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://monosnap.com/image/hOk7G1IDEdtHhSdEBwTu5ZK3rhLvHz)

不过还有一件事要做。我们将使用 [elm-phoenix](https://github.com/saschatimme/elm-phoenix) 库来处理 **Phoenix** sockets 通信，因此我们需要下载[这个文件夹](https://github.com/bigardone/passwordless-auth/tree/master/apps/passwordless_auth_web/assets/elm/vendor)的内容并将其放在`apps/passwordless_auth_web/assets/elm/vendor`文件夹中。

## 构建榆树应用程序

既然我们的基本 **Elm** 应用程序已经运行，让我们对负责渲染它的**凤凰**控制器和模板做一些修改。

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/controllers/page_controller.ex

defmodule PasswordlessAuthWeb.PageController do
  use PasswordlessAuthWeb, :controller

  def index(conn, params) do
    conn
    |> assign(:token, Map.get(params, "token", ""))
    |> render("index.html")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

第一个变化是将从认证链接接收到的`token`值分配给连接，这样我们就可以将它作为一个标志传递给 **Elm** 应用程序。我们还需要传递套接字 URL，所以让我们为此创建一个助手函数:

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/views/page_view.ex

defmodule PasswordlessAuthWeb.PageView do
  use PasswordlessAuthWeb, :view

  def socket_url do
    PasswordlessAuthWeb.Endpoint.url()
    |> String.replace("http", "ws")
    |> Kernel.<>("/socket/websocket")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在模板中设置这两个值:

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/templates/page/index.html.eex

<div id="elm-main" class="flex items-center justify-center h-screen">
</div>

<script>
  window.token = '<%= @token %>';
  window.socketUrl = '<%= socket_url() %>';
</script> 
```

Enter fullscreen mode Exit fullscreen mode

最后在 Elm 应用程序中将它们作为标志传递:

```
// apps/passwordless_auth_web/assets/js/app.js

import Elm from '../elm/src/Main.elm';
import css from '../css/app.css';

const elmDiv = document.getElementById('elm-main');

let token = window.token;
const socketUrl = window.socketUrl;

if (token === '' || token == null) token = window.localStorage.getItem('token');

if (elmDiv) {
  const app = Elm.Main.embed(elmDiv, { token, socketUrl });

  app.ports.saveToken.subscribe((token) => {
    window.localStorage.setItem('token', token);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还添加了一个端口，用于将令牌保存到`localStorage`中，我们稍后会用到这个端口。

在继续下一步之前，让我们用下面的模拟图来思考一下我们正在构建什么:

[![Routes](img/f05be39c47b525782ec78c876224f6b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N13VNPVB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://monosnap.com/image/Isrs18qTVpNMYfWcXNuq9RbvG7Z4nS.png)

为了测试我们的无密码认证实验，我们只需要几个屏幕和路径:

1.  只有经过认证的用户才能访问，并且可能有我们想要显示的任何私人数据。
2.  `/sign-in`是公共的，呈现请求神奇链接的表单，并且是我们重定向任何试图导航到`/`而没有正确认证的用户的地方。

考虑到这一点，我们可以从实现路由模块开始，这样我们就可以添加这两条路由:

```
-- apps/passwordless_auth_web/assets/elm/src/Route.elm

module Route
    exposing
        ( Route(..)
        , fromLocation
        , newUrl
        )

import Navigation exposing (Location)
import UrlParser as Url exposing ((</>), Parser, oneOf, s, string, parsePath)

-- ROUTING --

type Route
    = SignInRoute
    | LobbyRoute

matchers : Parser (Route -> a) a
matchers =
    oneOf
        [ Url.map LobbyRoute <| s ""
        , Url.map SignInRoute <| s "sign-in"
        ]

-- INTERNAL --

routeToString : Route -> String
routeToString page =
    let
        pieces =
            case page of
                LobbyRoute ->
                    []

                SignInRoute ->
                    ["sign-in"]
    in
        "/" ++ String.join "/" pieces

-- PUBLIC HELPERS --

newUrl : Route -> Cmd msg
newUrl =
    routeToString >> Navigation.newUrl

fromLocation : Location -> Maybe Route
fromLocation location =
    parsePath matchers location 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经定义了我们需要的路由，让我们继续到主 elm 模块，并开始编写应用程序。在我的许多 **Elm** 项目和教程中，我一直在文件夹中组织所有的 **Elm** 代码，为每条路线创建一个。在这些文件夹中，我倾向于添加四个不同的文件，分别对应于该路由的模型、消息、更新和视图模块。这种方法过去对小项目很有效，但对大项目就没那么有效了。然后我读了 [Richard Feldman 的](https://twitter.com/rtfeldman)文章[参观了一个开源的 Elm SPA](https://dev.to/rtfeldman/tour-of-an-open-source-elm-spa) ，我已经开始按照他的建议组织我的新 **Elm** 项目，现在当它们开始成长时，我感觉更舒服了。

### 页面模块

虽然这是一个非常小的项目，但我们将坚持 Richard 的方法，因此让我们从定义页面模块开始，这些模块定义了应用程序每个屏幕的逻辑，我们将在主模块中使用这些模块，这取决于应用程序中的当前页面集。

#### 页。登录

我们实现的第一个页面是登录页面。我们的目标是定义我们需要的一切，因为它是一个独立的 **Elm** 程序，除了它没有一个视图函数，入口点可以是一个从后端加载任何必要数据的 init 函数，也可以是它的初始模型:

```
-- apps/passwordless_auth_web/assets/elm/src/Page/SignIn.elm

module Page.SignIn
    exposing
        ( Model
        , SignInForm(..)
        , Msg(..)
        , update
        , initialModel
        )

import Http
import Json.Encode as Encode
import Json.Decode as Decode
import Request.Token exposing (requestToken)

-- MODEL --

type SignInForm
    = Editing String
    | Sending String
    | Success String
    | Error String

type alias Model =
    { form : SignInForm }

initialModel : Model
initialModel =
    { form = Editing "" }

-- UPDATE --

type Msg
    = HandleEmailInput String
    | HandleFormSubmit
    | FormSubmitResponse (Result Http.Error String)

update : Msg -> Model -> ( Model, Cmd Msg )
update msg ({ form } as model) =
    case ( msg, form ) of
        ( HandleEmailInput value, Editing _ ) ->
            { model | form = Editing value } ! []

        ( HandleFormSubmit, Editing email ) ->
            { model | form = Sending email } ! [requestToken FormSubmitResponse email]

        ( FormSubmitResponse payload, Sending _ ) ->
            case payload of
                Ok message ->
                    { model | form = Success message } ! []

                _ ->
                    { model | form = Error "We couldn't sent you your magic link due to an error, please try again later." } ! []

        _ ->
            model ! []

requestToken : (Result Http.Error String -> msg) -> String -> Cmd msg
requestToken msg email =
    let
        body =
            Encode.object [( "email", Encode.string email )]

        request =
            Http.request
                { method = "POST"
                , headers = []
                , url = "/api/auth"
                , body = Http.jsonBody body
                , expect = Http.expectJson requestTokenDecoder
                , timeout = Nothing
                , withCredentials = False
                }
    in
        Http.send msg request

requestTokenDecoder : Decode.Decoder String
requestTokenDecoder =
    Decode.at ["message"] Decode.string 
```

Enter fullscreen mode Exit fullscreen mode

这个页面的`Model`由一个带有`form`自定义类型的记录组成，它表示我们在登录表单中需要的不同状态:

*   当用户输入时，我们可以存储输入的值。
*   当用户提交表单时，例如，我们可以在提交按钮中呈现一个微调器。
*   `Success String`表示表单提交成功，以及从服务器收到的`String`。
*   如果出现任何问题，我们希望显示一条错误消息。

`update`函数也非常简单，处理以下消息:

*   `HandleEmailInput String`当表单邮件输入改变其值时，将其存储在模型中。
*   `HandleFormSubmit`发送`requestToken` HTTP 请求。
*   `FormSubmitResponse (Result Http.Error String)`它处理来自先前 HTTP 请求的响应。

#### 页。大厅

我们需要实现的另一个页面是我们希望只向经过身份验证的用户显示的页面:

```
-- apps/passwordless_auth_web/assets/elm/src/Page/Lobby.elm

module Page.Lobby exposing ( Model , Msg(..) , init , update)

import Json.Decode as Decode exposing (Value)
import Phoenix
import Phoenix.Push as Push exposing (Push)

-- MODEL --

type alias Model =
    { emails : List String }

initialModel : Model
initialModel =
    { emails = [] }

-- UPDATE --

type Msg
    = HandleInitSuccess Decode.Value

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        HandleInitSuccess payload ->
            case Decode.decodeValue usersDecoder payload of
                Ok emails ->
                    { model | emails = emails } ! []

                Err _ ->
                    model ! []

usersDecoder : Decode.Decoder (List String)
usersDecoder =
    Decode.at ["data"] <| Decode.list Decode.string

-- INIT --

init : String -> ( Model, Cmd Msg )
init socketUrl =
    ( initialModel
    , Push.init "admin:lobby" "data"
        |> Push.onOk HandleInitSuccess
        |> Phoenix.push socketUrl
    ) 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，`Model`由一个字符串列表组成，表示能够通过应用程序验证的电子邮件列表。每次通过验证的用户访问`/`路由时调用的`init`函数，接收到`socketUrl`并通过 socket(已经连接，一会儿就到)将`data`消息推送到`admin:lobby`通道。如果消息结果成功，它接收由`HandleInitSuccess`消息处理的电子邮件列表，解码该列表并将其设置为新的`Model`。

### 视图模块

主页面模块已经完成，现在让我们关注它们的视图模块。看一看`Lobby`页面，我们可以看到顶部有一个导航栏，带有注销链接。这个导航栏将出现在我们将来可能实现的所有私有屏幕中，所以我们需要以某种方式共享它。

#### 观点。页

让我们添加一个基本模块，将每个特定的视图内容和导航栏一起包装在一个容器中:

```
-- apps/passwordless_auth_web/assets/elm/src/Views/Page.elm

module Views.Page
    exposing
        ( frameView
        , headerView
        , Msg(..)
        )

import Data.Session exposing (Session(..))
import Html exposing (Html)
import Html.Attributes as Html
import Html.Events as Html

type Msg
    = SignOut

frameView : Session -> Html msg -> Html msg -> Html msg
frameView session header content =
    case session of
        Anonymous ->
            Html.text ""

        Authenticated _ ->
            Html.div
                [Html.class "main-section flex-1 flex-col flex h-screen"]
                [ header
                , Html.div
                    [Html.class "main-content bg-grey-lightest flex-1 flex items-center justify-center"]
                    [content]
                ]

headerView : Html Msg
headerView =
    Html.header
        [Html.class "main-header"]
        [ Html.nav
            [Html.class "flex justify-between"]
            [ Html.span
                [Html.class "flex-1 p-4 text-white text-left"]
                [Html.text "Admin panel"]
            , Html.a
                [ Html.class "p-4"
                , Html.onClick SignOut
                ]
                [Html.text "Sign out"]
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

它公开了两个功能:

*   `frameView`它定义了所有私人屏幕的结构。
*   `headerView`呈现顶部导航栏，带有注销链接。

`frameView`需要一个`Session`，所以我们可以只在用户被认证时才呈现内容，以及它所呈现的特定页面的`header`和`content`。

#### 观点。登录

让我们为`SignIn`页面添加视图模块。

```
-- apps/passwordless_auth_web/assets/elm/src/Views/SignIn.elm

module Views.SignIn exposing (view)

import Html exposing (Html, form)
import Html.Attributes as Html
import Html.Events as Html
import Page.SignIn exposing (Model, SignInForm(..), Msg(..))

view : Model -> Html Msg
view { form } =
    let
        content =
            case form of
                Editing email ->
                    formView email

                Sending email ->
                    formView email

                Success text ->
                    successMessageView text

                Error text ->
                    errorMessageView text
    in
        Html.section
            [Html.class "bg-purple-darker p-8 flex flex-1 items-center justify-center h-screen"]
            [content]

formView : String -> Html Msg
formView email =
    Html.div
        []
        [ Html.img
            [ Html.srcimg/icons8-mailbox-128.png"
            , Html.class "mb-4 slide-in-blurred-top"
            ]
            []
        , Html.h3
            []
            [Html.text "Password long? Hard to type?"]
        , Html.p
            []
            [Html.text "Get a magic link sent to your email that'll sign you instantly!"]
        , form
            [ Html.class "w-full max-w-md"
            , Html.onSubmit HandleFormSubmit
            ]
            [ Html.input
                [ Html.class "appearance-none block w-full bg-grey-lighter text-grey-darker rounded py-3 px-4 mb-3"
                , Html.placeholder "foo@email.com"
                , Html.type_ "email"
                , Html.onInput HandleEmailInput
                , Html.value email
                ]
                []
            , Html.button
                [Html.class "bg-blue hover:bg-blue-dark text-white py-3 px-4 mb-3 rounded w-full"]
                [Html.text "Send Magic Link"]
            ]
        ]

successMessageView : String -> Html Msg
successMessageView message =
    Html.div
        []
        [ Html.img
            [ Html.srcimg/icons8-postal-128.png"
            , Html.class "mb-4 jello-horizontal"
            ]
            []
        , Html.h3
            []
            [Html.text "Check your email"]
        , Html.p
            []
            [Html.text message]
        ]

errorMessageView : String -> Html Msg
errorMessageView message =
    Html.div
        []
        [ Html.h3
            []
            [Html.text "Whoops!"]
        , Html.p
            []
            [Html.text message]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

`view`函数返回要呈现的内容，这取决于我们之前为模型表单创建的自定义类型的值。

#### 查看。大厅

我们要实现的下一个视图是用于`Lobby`页面的视图，所以让我们继续添加它:

```
-- apps/passwordless_auth_web/assets/elm/src/Views/Lobby.elm

module Views.Lobby exposing (view)

import Html exposing (Html, form)
import Html.Keyed
import Html.Attributes as Html
import Data.Session exposing (User)
import Page.Lobby exposing (Model, Msg(..))

view : User -> Model -> Html Msg
view { email } model =
    Html.div
        []
        [ Html.img
            [ Html.srcimg/icons8-confetti-128.png"
            , Html.class "mb-4 jello-horizontal"
            ]
            []
        , Html.h3
            []
            [Html.text <| "Welcome " ++ email ++ "!"]
        , Html.p
            []
            [Html.text "You signed in successfully."]
        , Html.p
            []
            [Html.text "List of authorized users:"]
        , model
            |> List.map emailView
            |> Html.Keyed.ul [Html.class "inline-block m-0 p-0"]
        ]

emailView : String -> ( String, Html Msg )
emailView email =
    ( email
    , Html.li
        []
        [Html.text email]
    ) 
```

Enter fullscreen mode Exit fullscreen mode

它的`view`函数接收当前的`User`和它的模型，后者由一个电子邮件列表组成，并在屏幕上呈现它们。

#### 查看。未找到

最后，让我们添加视图来呈现不存在的页面，以防用户访问错误的路径:

```
-- apps/passwordless_auth_web/assets/elm/src/Views/NotFound.elm

module Views.NotFound exposing (view)

import Html exposing (Html, form)

view : Html msg
view =
    Html.div
        []
        [ Html.h1
            []
            [Html.text "404"]
        , Html.p
            []
            [Html.text "Page not found"]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

### 主模块

有了特定于页面的逻辑和视图，我们可以开始编辑`Main`模块。由于这个模块相当大，让我们把它分成不同的代码块。

#### 模型截面

```
module Main exposing (main)

import Data.Session exposing (Session(..))
import Html exposing (Html, form, map)
import Json.Decode as Decode exposing (Value)
import Navigation exposing (Location)
import Page.Lobby
import Page.SignIn
import Phoenix
import Phoenix.Channel as Channel exposing (Channel)
import Phoenix.Push as Push exposing (Push)
import Phoenix.Socket as Socket exposing (AbnormalClose, Socket)
import Ports
import Route exposing (Route(..))
import Views.Lobby
import Views.Page
import Views.SignIn

-- MODEL --

type ConnectionStatus
    = Connecting
    | Connected ChannelState
    | Disconnected

type ChannelState
    = Joining
    | Joined
    | Left

type Page
    = BlankPage
    | NotFoundPage
    | SignInPage Page.SignIn.Model
    | LobbyPage Page.Lobby.Model

type alias Flags =
    { token : Maybe String
    , socketUrl : String
    }

type alias Model =
    { page : Page
    , session : Session
    , connectionStatus : ConnectionStatus
    , flags : Flags
    }
-- ... 
```

Enter fullscreen mode Exit fullscreen mode

主`Model`包括:

*   `page`表示当前`Page`状态的自定义类型。
*   `session`它保存了关于认证用户的信息。
*   `connectionStatus`这是另一个自定义类型，表示套接字连接的状态和通道状态。
*   `flags`存储初始化应用程序时从外部传来的配置数据。

#### 更新章节

```
-- UPDATE --

type Msg
    = SetRoute (Maybe Route)
    | ConnectionStatusChanged ConnectionStatus
    | HandleAdminChannelJoin Decode.Value
    | ViewsPageMsg Views.Page.Msg
    | HandleSignOutSuccess Decode.Value
    | PageSignInMsg Page.SignIn.Msg
    | PageLobbyMsg Page.Lobby.Msg

update : Msg -> Model -> ( Model, Cmd Msg )
update msg ({ flags, page } as model) =
    let
        { socketUrl } =
            flags

        toPage toModel toMsg subUpdate subMsg subModel =
            let
                ( newModel, newCmd ) =
                    subUpdate subMsg subModel
            in
            ( { model | page = toModel newModel }, Cmd.map toMsg newCmd )
    in
    case ( msg, page ) of
        ( SetRoute route, _ ) ->
            setRoute route model

        ( ConnectionStatusChanged connectionStatus, _ ) ->
            let
                cmds =
                    case connectionStatus of
                        Disconnected ->
                            [ Route.newUrl SignInRoute
                            , Ports.saveToken Nothing
                            ]

                        _ ->
                            []
            in
            { model | connectionStatus = connectionStatus } ! cmds

        ( HandleAdminChannelJoin payload, _ ) ->
            case Decode.decodeValue Data.Session.decoder payload of
                Ok user ->
                    { model
                        | session =
                            Authenticated user
                        , connectionStatus = Connected Joined
                    }
                        ! [Ports.saveToken model.flags.token]

                Err error ->
                    let
                        _ =
                            Debug.log "Error" error
                    in
                    model ! []

        ( HandleSignOutSuccess _, _ ) ->
            { model
                | connectionStatus = Disconnected
                , session = Anonymous
            }
                ! [Route.newUrl SignInRoute, Ports.saveToken Nothing]

        ( ViewsPageMsg subMsg, _ ) ->
            case subMsg of
                Views.Page.SignOut ->
                    model
                        ! [ Push.init "admin:lobby" "sign_out"
                                |> Push.onOk HandleSignOutSuccess
                                |> Phoenix.push socketUrl
                          ]

        ( ViewsPageMsg subMsg, _ ) ->
            case subMsg of
                Views.Page.SignOut ->
                    model
                        ! [ Push.init "admin:lobby" "sign_out"
                                |> Push.onOk HandleSignOutSuccess
                                |> Phoenix.push socketUrl
                          ]

        ( PageSignInMsg subMsg, SignInPage subModel ) ->
            toPage SignInPage PageSignInMsg Page.SignIn.update subMsg subModel

        ( PageLobbyMsg subMsg, LobbyPage subModel ) ->
            toPage LobbyPage PageLobbyMsg Page.Lobby.update subMsg subModel

        ( _, _ ) ->
            model ! []

setRoute : Maybe Route -> Model -> ( Model, Cmd Msg )
setRoute maybeRoute ({ connectionStatus, session, flags } as model) =
    let
        transition init page toMsg =
            let
                ( subModel, subCmd ) =
                    init
            in
            { model | page = page subModel } ! [Cmd.map toMsg subCmd]
    in
    case maybeRoute of
        Nothing ->
            { model | page = NotFoundPage } ! [Cmd.none]

        Just SignInRoute ->
            { model | page = SignInPage Page.SignIn.initialModel } ! [Cmd.none]

        Just LobbyRoute ->
            case connectionStatus of
                Disconnected ->
                    model ! [Route.newUrl SignInRoute]

                _ ->
                    transition (Page.Lobby.init flags.socketUrl) LobbyPage PageLobbyMsg 
```

Enter fullscreen mode Exit fullscreen mode

`update`函数评估当前的`msg`和`page`，并根据组合采取以下动作之一:

*   `( SetRoute route, _ )`表示路由改变，因此它调用`setRoute`函数根据新路由初始化页面状态。
*   `( ConnectionStatusChanged connectionStatus, _ )`处理 socket 连接的变化，调用端口从`localStorage`删除存储的认证令牌并重定向到`SignInRoute`路由，在模型中设置新的连接状态。
*   `( HandleAdminChannelJoin payload, _ )`处理成功加入管理通道，将用户的数据设置到模型的`session`键中，并将连接状态更改为正确的状态。
*   `( HandleSignOutSuccess _, _ )`设置`connectionStatus`为断开，重置会话用户为`Nothing`，调用端口删除令牌，重定向到`SignInRoute`路由。
*   `( ViewsPageMsg subMsg, _ )`检查通过点击标题的注销链接发送的`Views.Page.SignOut`消息，并通过套接字向管理频道发送一条`sign_out`消息。
*   `( PageSignInMsg subMsg, SignInPage subModel )`和`( PageLobbyMsg subMsg, LobbyPage subModel )`使用它们相应的更新函数更新当前页面状态。

`setRoute`函数负责两件不同的事情:

1.  它初始化当前的路由页面模型。
2.  如果用户没有连接到套接字，则重定向到`SignInRoute`路由，这意味着没有经过身份验证。

#### 查看截面

```
-- VIEW --

view : Model -> Html Msg
view { session, page } =
    let
        header =
            Html.map ViewsPageMsg Views.Page.headerView

        frame =
            Views.Page.frameView session header
    in
    case ( page, session ) of
        ( SignInPage subModel, _ ) ->
            map PageSignInMsg (Views.SignIn.view subModel)

        ( LobbyPage subModel, Authenticated user ) ->
            frame <| map PageLobbyMsg (Views.Lobby.view user subModel)

        ( NotFoundPage, Authenticated _ ) ->
            frame Views.NotFound.view

        _ ->
            frame <| Html.text "View not implemented yet" 
```

Enter fullscreen mode Exit fullscreen mode

`view`函数获取当前模型，并根据当前的`page`和`session`渲染必要的视图，是否用`frame`包裹它。

#### 订阅部分

```
-- SUBSCRIPTIONS --

socket : Flags -> Socket Msg
socket { socketUrl, token } =
    Socket.init socketUrl
        |> Socket.withParams [( "token", Maybe.withDefault "" token )]
        |> Socket.onOpen (ConnectionStatusChanged (Connected Joining))
        |> Socket.onClose (\_ -> ConnectionStatusChanged Disconnected)
        |> Socket.withDebug

subscriptions : Model -> Sub Msg
subscriptions { connectionStatus, flags } =
    let
        { token } =
            flags

        adminChannel =
            Channel.init "admin:lobby"
                |> Channel.onJoin HandleAdminChannelJoin
                |> Channel.withDebug
    in
    case connectionStatus of
        Disconnected ->
            Sub.none

        _ ->
            Phoenix.connect (socket flags) [adminChannel] 
```

Enter fullscreen mode Exit fullscreen mode

根据`connectionStatus`，它尝试连接到**凤凰** `Socket`，如果服务器端认证成功，将状态更改为`Connected Joining`。相反，它将它设置为`Disconnected`,这将重定向到我们之前看到的登录页面。

#### 主段和初始化段

```
-- MAIN AND INIT --

init : Flags -> Location -> ( Model, Cmd Msg )
init flags location =
    let
        ( model, cmd ) =
            setRoute (Route.fromLocation location)
                { page = BlankPage
                , session = Anonymous
                , connectionStatus = Connecting
                , flags = flags
                }
    in
    model ! [cmd]

main : Program Flags Model Msg
main =
    Navigation.programWithFlags (Route.fromLocation >> SetRoute)
        { init = init
        , view = view
        , update = update
        , subscriptions = subscriptions
        } 
```

Enter fullscreen mode Exit fullscreen mode

当`main`返回一个`Navigation.programWithFlags`应用时，`init`函数带着接收到的`Flags`和一个`Location`，根据当前路径调用`setRoute`初始化应用的状态。

### 附加的东西

除了两个缺失的模块之外，我们几乎涵盖了所有内容:

#### 端口模块

```
-- apps/passwordless_auth_web/assets/elm/src/Ports.elm

port module Ports exposing (saveToken)

port saveToken : Maybe String -> Cmd msg 
```

Enter fullscreen mode Exit fullscreen mode

`saveToken`是一个端口，它将有效的认证令牌或空令牌发送到主`app.js`脚本，并在那里使用`localStorage`进行存储。这种行为的原因是，如果用户刷新浏览器，我们希望重用有效的令牌来重新连接，或者在用户退出时删除它。

#### 数据。会话模块

因为我们到处都在使用`Session`，并且为了防止循环依赖，让所有与`Session`相关的东西都在它自己的模块中是有意义的。

```
-- apps/passwordless_auth_web/assets/elm/src/Data/Session.elm

module Data.Session exposing
    ( Session(..)
    , User
    , decoder
    , encode
    )

import Json.Decode as Decode exposing (Decoder)
import Json.Decode.Pipeline exposing (decode, required)
import Json.Encode as Encode exposing (Value)

type alias User =
    { email : String }

type Session
    = Anonymous
    | Authenticated User

-- SERIALIZATION --

decoder : Decoder User
decoder =
    decode User
        |> required "email" Decode.string

encode : User -> Decode.Value
encode user =
    Encode.object
        [ ( "email", Encode.string user.email )
        ] 
```

Enter fullscreen mode Exit fullscreen mode

我们再次依赖自定义类型来定义`Session`，它可以是`Anonymous`或`Authenticated User`。

### 凤凰管理频道

如果我们启动应用程序，我们可以看到与套接字连接相关的错误。这是因为我们还没有创建`AdminChannel`，所以让我们继续将它的定义添加到`UserSocket`模块:

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/channels/user_socket.ex

defmodule PasswordlessAuthWeb.UserSocket do
  # ...

  channel("admin:*", PasswordlessAuthWeb.AdminChannel)

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，频道模块:

```
# apps/passwordless_auth_web/lib/passwordless_auth_web/channels/admin_channel.ex

defmodule PasswordlessAuthWeb.AdminChannel do
  use PasswordlessAuthWeb, :channel

  require Logger

  alias PasswordlessAuth.Repo

  def join("admin:lobby", _payload, socket) do
    {:ok, socket.assigns.user, socket}
  end

  def handle_in("data", _, socket) do
    admin_emails = Application.get_env(:passwordless_auth, :repo)[:emails]

    {:reply, {:ok, %{data: admin_emails}}, socket}
  end

  def handle_in("sign_out", _, socket) do
    email = socket.assigns.user.email
    :ok = Repo.invalidate(email)

    {:reply, {:ok, %{success: true}}, socket}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

该模块的逻辑非常简单:

*   `join`总是返回成功和分配给连接的认证用户数据。
*   `handle_in("data", _, _)`返回有效电子邮件的现有列表，以便我们可以在大厅视图中呈现它。
*   `handle_in("sign_out", _, _)`接收分配的电子邮件并使其在存储库中的令牌失效，因此无法再次使用。

差不多就是这样了。我们终于有了基本的 **Elm** SPA 管理面板，背后是 **Phoenix** 无密码和无数据库系统。当我写这篇文章时，我已经重构了应用程序的许多部分，所以我可能错过了一些变化。因此，不要忘记检查缺失部分的源代码，并对任何不便表示歉意。 **Elm 0.19.0** 刚出来，所以我打算写一个关于升级它的新部分，一旦它对 Websockets 有了适当的支持。

编码快乐！

[源代码](https://github.com/bigardone/passwordless-auth)
[![](img/1c0813281f9983b25823b768bdf2c1cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wtshTk4E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/CodeLoveAndBoards/%257E4/lXn38etaNYA)