# Elixir API 和 Elm SPA -第 4 部分

> 原文：<https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-4-4il0>

# 第 4 部分:添加登录和注册页面

我们将添加一个应用程序的登录和注册页面。

## 系列

1.  [第 1 部分- Elixir 应用程序创建](https://dev.to/miguelcoba/elixir-api-and-elm-spa-4hpf)
2.  [第 2 部分-增加监护人认证](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-2-1pca)
3.  [第 3 部分- Elm 应用程序创建和路由设置](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-3-3n0h)
4.  第 4 部分-添加登录和注册页面
5.  [第 5 部分:将会话数据保存到本地存储器](https://dev.to/miguelcoba/elixir-api-and-elm-spa---part-5-4j1k)

## 将 CORS 添加到后端 api

在我们开始向我们的后端 api 发送请求之前，我们需要启用 CORS，以便我们的后端允许请求通过。因此，请访问 toltec-api 网站，向 mix.exs 文件添加一个新的依赖项:

```
# mix.exs

 defp deps do
    [
      {:phoenix, "~> 1.3.0"},
      {:phoenix_pubsub, "~> 1.0"},
      {:phoenix_ecto, "~> 3.2"},
      {:postgrex, ">= 0.0.0"},
      {:gettext, "~> 0.11"},
      {:cowboy, "~> 1.0"},
      {:comeonin, "~> 4.0"},
      {:argon2_elixir, "~> 1.2"},
      {:guardian, "~> 1.0"},
      {:cors_plug, "~> 1.4"}
    ]
  end 
```

Enter fullscreen mode Exit fullscreen mode

然后将其添加到 endpoint.ex，就在路由器插入之前

```
# lib/toltec_web/endpoint.ex

  plug(CORSPlug)

  plug(ToltecWeb.Router) 
```

Enter fullscreen mode Exit fullscreen mode

使用 mix deps.get 获取依赖项并重启 toltec-api 应用程序。我们应该准备好消费 API。

## 添加榆树依赖关系

我们将需要一些外部包来构建我们的应用程序。添加它们:

```
elm-app install NoRedInk/elm-decode-pipeline
elm-app install elm-community/json-extra
elm-app install elm-lang/http
elm-app install rtfeldman/elm-validate 
```

Enter fullscreen mode Exit fullscreen mode

## 创建用户模型

我们还需要一个用户模型。添加一个新的用户/目录并创建一个 Model.elm

```
-- src/User/Model.elm

module User.Model exposing (User, decoder, encode)

import Json.Decode as Decode exposing (Decoder)
import Json.Decode.Pipeline exposing (decode, required, optional)
import Json.Encode as Encode exposing (Value)
import Json.Encode.Extra as Extra exposing (maybe)
import Util exposing ((=>))

type alias User =
    { email : String
    , name : Maybe String
    }

decoder : Decoder User
decoder =
    decode User
        |> required "email" Decode.string
        |> required "name" (Decode.nullable Decode.string)

encode : User -> Value
encode user =
    Encode.object
        [ "email" => Encode.string user.email
        , "name" => (Extra.maybe Encode.string) user.name
        ] 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么奇怪的。只是一个模型一对编码器/解码器*函数*来序列化/反序列化这个模型。

## 创建会话模型

在会话/目录
中创建一个 Model.elm 文件

```
-- src/Session/Model.elm

module Session.Model exposing (Session, decoder, encode)

import Session.AuthToken as AuthToken exposing (AuthToken, decoder)
import User.Model as User exposing (User, decoder)
import Json.Decode as Decode exposing (Decoder)
import Json.Decode.Pipeline exposing (decode, required, optional)
import Json.Encode as Encode exposing (Value)
import Util exposing ((=>))

type alias Session =
    { user : User
    , token : AuthToken
    }

decoder : Decoder Session
decoder =
    decode Session
        |> required "user" User.decoder
        |> required "token" AuthToken.decoder

encode : Session -> Value
encode session =
    Encode.object
        [ "user" => User.encode session.user
        , "token" => AuthToken.encode session.token
        ] 
```

Enter fullscreen mode Exit fullscreen mode

会话是一种带有用户和令牌的类型，令牌是我们从后端 API 获得的，并且我们需要在每次向 REST API 发出请求时发送。
authtoken . elm 是这样的:

```
-- src/Session/AuthToken.elm

module Session.AuthToken exposing (AuthToken, decoder, encode)

import Json.Decode as Decode exposing (Decoder)
import Json.Encode as Encode exposing (Value)

type AuthToken
    = AuthToken String

encode : AuthToken -> Value
encode (AuthToken token) =
    Encode.string token

decoder : Decoder AuthToken
decoder =
    Decode.string
        |> Decode.map AuthToken 
```

Enter fullscreen mode Exit fullscreen mode

AuthToken 非常简单，它是一个标记类型，用来保存我们成功登录时获得的令牌。

## 创建登录页面

该登录是 Richard 登录页面的直接副本。想法是这样的:你有一个自包含的模块，它完成自己的内部模型-更新-视图循环。这将由我们已经拥有的外部*更新*功能驱动。这个内部循环将通过返回一个元组与外部循环进行通信，该元组带有一条指示发生了什么的消息。此消息是 ExternalMsg。除此之外，内循环对如何使用一无所知。

让我们首先将模型添加到 Login.elm 文件:

```
-- src/Session/Login.elm

-- MODEL --

type alias Model =
    { errors : List Error
    , email : String
    , password : String
    }

initialModel : Model
initialModel =
    { errors = []
    , email = ""
    , password = ""
    } 
```

Enter fullscreen mode Exit fullscreen mode

这非常简单，我们有一个电子邮件和密码，以及一个验证错误列表显示给用户。

现在用下面的代码替换我们在同一个文件中的当前视图:

```
-- src/Session/Login.elm

-- VIEW --

view : Model -> Html Msg
view model =
    div [ class "mt4 mt6-l pa4" ]
        [ h1 [] [ text "Sign in" ]
        , div [ class "measure center" ]
            [ Form.viewErrors model.errors
            , viewForm
            ]
        ]

viewForm : Html Msg
viewForm =
    Html.form [ onSubmit SubmitForm ]
        [ Form.input "Email" [ onInput SetEmail ] []
        , Form.password "Password" [ onInput SetPassword ] []
        , button [ class "b ph3 pv2 input-reset ba b--black bg-transparent grow pointer f6" ] [ text "Sign in" ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

添加消息:

```
-- src/Session/Login.elm

-- MESSAGES --

type Msg
    = SubmitForm
    | SetEmail String
    | SetPassword String
    | LoginCompleted (Result Http.Error Session)

type ExternalMsg
    = NoOp
    | SetSession Session 
```

Enter fullscreen mode Exit fullscreen mode

这里最主要的是 ExternalMsg，它将用于与外部的 *update* 函数进行通信。

添加登录的内部*更新*功能。注意返回类型，包括向外部世界发送重要信息的外部类型。

```
-- src/Session/Login.elm

-- UPDATE --

update : Msg -> Model -> ( ( Model, Cmd Msg ), ExternalMsg )
update msg model =
    case msg of
        SubmitForm ->
            case validate modelValidator model of
                [] ->
                    { model | errors = [] }
                        => Http.send LoginCompleted (login model)
                        => NoOp

                errors ->
                    { model | errors = errors }
                        => Cmd.none
                        => NoOp

        SetEmail email ->
            { model | email = email }
                => Cmd.none
                => NoOp

        SetPassword password ->
            { model | password = password }
                => Cmd.none
                => NoOp

        LoginCompleted (Err error) ->
            let
                errorMessages =
                    case error of
                        Http.BadStatus response ->
                            response.body
                                |> decodeString (field "errors" errorsDecoder)
                                |> Result.withDefault []

                        _ ->
                            [ "unable to perform login" ]
            in
                { model | errors = List.map (\errorMessage -> Form => errorMessage) errorMessages }
                    => Cmd.none
                    => NoOp

        LoginCompleted (Ok session) ->
            model
                => Route.modifyUrl Route.Home
                => SetSession session 
```

Enter fullscreen mode Exit fullscreen mode

我们需要验证表格是否正确。添加:

```
-- src/Session/Login.elm

-- VALIDATION --

type Field
    = Form
    | Email
    | Password

type alias Error =
    ( Field, String )

modelValidator : Validator Error Model
modelValidator =
    Validate.all
        [ ifBlank .email (Email => "email can't be blank.")
        , ifBlank .password (Password => "password can't be blank.")
        ] 
```

Enter fullscreen mode Exit fullscreen mode

我们使用解码器从后端发送给我们的 JSON 响应中提取错误。

```
-- src/Session/Login.elm

-- DECODERS --

errorsDecoder : Decoder (List String)
errorsDecoder =
    decode (\email password error -> error :: List.concat [ email, password ])
        |> optionalFieldError "email"
        |> optionalFieldError "password"
        |> optionalError "error" 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，它在电子邮件、密码或一般错误属性下查找错误。

你的导入应该是这样的:

```
-- src/Session/Login.elm

module Session.Login exposing (ExternalMsg(..), Model, Msg, initialModel, update, view)

import Helpers.Decode exposing (optionalError, optionalFieldError)
import Helpers.Form as Form
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (..)
import Http
import Json.Decode as Decode exposing (Decoder, decodeString, field, string)
import Json.Decode.Pipeline exposing (decode)
import Route exposing (Route)
import Session.Model exposing (Session)
import Session.Request exposing (login)
import Util exposing ((=>))
import Validate exposing (Validator, ifBlank, validate) 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里使用了一些辅助函数。创建一个新的 Helpers/目录并添加这些文件:

```
-- src/Helpers/Decode.elm

module Helpers.Decode exposing (optionalError, optionalFieldError)

import Json.Decode as Decode exposing (Decoder, decodeString, field, string)
import Json.Decode.Pipeline exposing (decode, optional)

optionalError : String -> Decoder (String -> a) -> Decoder a
optionalError fieldName =
    optional fieldName string ""

optionalFieldError : String -> Decoder (List String -> a) -> Decoder a
optionalFieldError fieldName =
    let
        errorToString errorMessage =
            String.join " " [ fieldName, errorMessage ]
    in
        optional fieldName (Decode.list (Decode.map errorToString string)) [] 
```

Enter fullscreen mode Exit fullscreen mode

这两个函数简化了 JSON 响应中缺失或可选属性的处理。

```
-- src/Helpers/Form.elm

module Helpers.Form exposing (input, password, textarea, viewErrors)

import Html exposing (Attribute, Html, fieldset, li, text, ul, label)
import Html.Attributes as Attr exposing (class, type_, name)

password : String -> List (Attribute msg) -> List (Html msg) -> Html msg
password name attrs =
    control Html.input name ([ type_ "password" ] ++ attrs)

input : String -> List (Attribute msg) -> List (Html msg) -> Html msg
input name attrs =
    control Html.input name ([ type_ "text" ] ++ attrs)

textarea : String -> List (Attribute msg) -> List (Html msg) -> Html msg
textarea name =
    control Html.textarea name

viewErrors : List ( a, String ) -> Html msg
viewErrors errors =
    errors
        |> List.map (\( _, error ) -> li [ class "dib" ] [ text error ])
        |> ul [ class "ph2 tl f6 red measure" ]

control :
    (List (Attribute msg) -> List (Html msg) -> Html msg)
    -> String
    -> List (Attribute msg)
    -> List (Html msg)
    -> Html msg
control element name attributes children =
    fieldset [ class "ba b--transparent ph0 mh0 f6" ]
        [ label [ class "db fw6 lh-copy tl" ] [ text name ]
        , element
            ([ Attr.name name, class "pa2 input-reset ba bg-transparent w-100" ] ++ attributes)
            children
        ] 
```

Enter fullscreen mode Exit fullscreen mode

这有一些方便的函数来为我们的应用程序中的表单创建输入字段。

```
-- src/Helpers/Request.elm

module Helpers.Request exposing (apiUrl)

apiUrl : String -> String
apiUrl str =
    "http://localhost:4000/api" ++ str 
```

Enter fullscreen mode Exit fullscreen mode

我们继续。这里还缺少一些东西:完成请求的部分。在 *update* 函数内的 SubmitForm 分支上，我们使用 Http.send 向后端发送请求，并指示它用 LoginCompleted 消息标记响应。这是请求的定义:

```
-- src/Session/Request.elm

module Session.Request exposing (login)

import Session.AuthToken as AuthToken exposing (AuthToken)
import User.Model as User exposing (User)
import Session.Model as Session exposing (Session)
import Http
import Json.Decode as Decode exposing (Decoder)
import Json.Encode as Encode
import Helpers.Request exposing (apiUrl)
import Util exposing ((=>))

login : { r | email : String, password : String } -> Http.Request Session
login { email, password } =
    let
        user =
            Encode.object
                [ "email" => Encode.string email
                , "password" => Encode.string password
                ]

        body =
            user
                |> Http.jsonBody
    in
        decodeSessionResponse
            |> Http.post (apiUrl "/sessions") body

decodeSessionResponse : Decoder Session
decodeSessionResponse =
    Decode.map2 Session
        (Decode.field "data" User.decoder)
        (Decode.at [ "meta", "token" ] AuthToken.decoder) 
```

Enter fullscreen mode Exit fullscreen mode

登录消息接收一个带有电子邮件和密码的[可扩展记录](https://medium.com/@ckoster22/advanced-types-in-elm-extensible-records-67e9d804030d),并用它们构建一个 JSON 主体。然后，这个主体被发送到 apiUrl 上的“/sessions”路径。

我们还指示 Http.post 函数使用 decodeSessionResponse 对我们从 API 获得的响应进行解码，并将其映射到会话类型，对响应的每个子部分使用适当的解码器。正如我们在前面的部分中看到的，用户数据来自“data”属性，JWT 来自“meta”属性下面的“token”属性。

总之，如果 API 的响应成功，Http.post 会给我们一个会话类型。Http.send 将使用 LoginCompleted 标记请求的结果。如果确定，它将是会话类型，如果不确定，它将是一个错误字符串。如你所见，我们在*更新*函数中匹配了这两种情况。

修改 Model.elm 以使用我们刚刚创建的会话:

```
-- src/Model.elm

import Session.Login as Login
import Session.Model as Session exposing (Session)

-- ...

type Page
    = Blank
    | NotFound
    | Home
    | Login Login.Model
    | Register

-- ...

type alias Model =
    { session : Maybe Session
    , pageState : PageState
    }

initialModel : Value -> Model
initialModel val =
    { session = Nothing
    , pageState = Loaded Blank
    } 
```

Enter fullscreen mode Exit fullscreen mode

将 LoginMsg 消息添加到 Messages.elm

```
-- src/Messages.elm

import Session.Login as Login

-- ...

type Msg
    = SetRoute (Maybe Route)
    | LoginMsg Login.Msg 
```

Enter fullscreen mode Exit fullscreen mode

更改 Update.elm:
中的 *updateRoute* 函数

```
-- src/Update.elm

-- ...

import Session.Login as Login

-- ...

-- From this
Just Route.Login ->
    { model | pageState = Loaded Login } => Cmd.none

-- To this
Just Route.Login ->
    { model | pageState = Loaded (Login Login.initialModel) } => Cmd.none 
```

Enter fullscreen mode Exit fullscreen mode

添加新的分支到*更新页面*功能:

```
-- src/Update.elm

updatePage : Page -> Msg -> Model -> ( Model, Cmd Msg )
updatePage page msg model =
    case ( msg, page ) of
        ( SetRoute route, _ ) ->
            updateRoute route model

        ( LoginMsg subMsg, Login subModel ) ->
            let
                ( ( pageModel, cmd ), msgFromPage ) =
                    Login.update subMsg subModel

                newModel =
                    case msgFromPage of
                        Login.NoOp ->
                            model

                        Login.SetSession session ->
                            { model | session = Just session }
            in
                { newModel | pageState = Loaded (Login pageModel) }
                    => Cmd.map LoginMsg cmd

        ( _, NotFound ) ->
            -- Disregard incoming messages when we're on the
            -- NotFound page.
            model => Cmd.none

        ( _, _ ) ->
            -- Disregard incoming messages that arrived for the wrong page
            model => Cmd.none 
```

Enter fullscreen mode Exit fullscreen mode

现在改变 View.elm
中的 *viewPage* 函数

```
-- src/View.elm
-- ...

import Session.Login as Login

-- ...

-- From this
Login ->
    Login.view
        |> frame Page.Login

-- To this
Login subModel ->
    Login.view subModel
        |> frame Page.Login
        |> Html.map LoginMsg 
```

Enter fullscreen mode Exit fullscreen mode

以及 Subscriptions.elm
中的*页面订阅*函数

```
-- src/Subscriptions.elm

-- From this
Login ->
    Sub.none

-- To this
Login _ ->
    Sub.none 
```

Enter fullscreen mode Exit fullscreen mode

就这样，登录页面现在应该可以工作了。 *elm-app 启动*你的应用，你应该看不到任何错误。如果您打开浏览器，应该会看到类似这样的内容:

[![Toltec login page showing the email and password form](img/78600008fd44c62e7467cbcb7e34738d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MwXcFIez--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tkk5z450miy7gdo3ytkj.png)

使用我们的 seed 用户(电子邮件:user@toltec，密码:user@toltec)登录后，您应该会看到主页:

[![Toltec home page after successful login](img/5e8c597e10abc3e4a5715c53ac4a7857.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--74uc0YYJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8e9q4c63acew7f0m2s9y.png)

## 创建注册页面

让我们创建注册页面。这与登录页面几乎完全相同，因此我们不打算在这里详细介绍。

从修改模型
开始

```
-- src/Model.elm

import Session.Register as Register

-- ...

type Page
    = Blank
    | NotFound
    | Home
    | Login Login.Model
    | Register Register.Model 
```

Enter fullscreen mode Exit fullscreen mode

消息

```
-- src/Messages.elm

-- ...
import Session.Register as Register

type Msg
    = SetRoute (Maybe Route)
    | LoginMsg Login.Msg
    | RegisterMsg Register.Msg 
```

Enter fullscreen mode Exit fullscreen mode

和订阅

```
-- src/Subscriptions.elm

-- From this
Register ->
    Sub.none

-- To this
Register _ ->
    Sub.none 
```

Enter fullscreen mode Exit fullscreen mode

更新路由功能

```
-- src/Update.elm

-- ...

import Session.Register as Register

-- ...

-- From this
Just Route.Register ->
    { model | pageState = Loaded Register } => Cmd.none

-- To this
Just Route.Register ->
    { model | pageState = Loaded (Register Register.initialModel) } => Cmd.none 
```

Enter fullscreen mode Exit fullscreen mode

和更新页面功能

```
-- src/Update.elm

updatePage : Page -> Msg -> Model -> ( Model, Cmd Msg )
updatePage page msg model =
    case ( msg, page ) of
        -- ..

        ( RegisterMsg subMsg, Register subModel ) ->
            let
                ( ( pageModel, cmd ), msgFromPage ) =
                    Register.update subMsg subModel

                newModel =
                    case msgFromPage of
                        Register.NoOp ->
                            model

                        Register.SetSession session ->
                            { model | session = Just session }
            in
                { newModel | pageState = Loaded (Register pageModel) }
                    => Cmd.map RegisterMsg cmd

        -- .. 
```

Enter fullscreen mode Exit fullscreen mode

视图

```
-- src/View.elm

-- From this
Register ->
    Register.view
        |> frame Page.Register

-- To this
Register subModel ->
    Register.view subModel
        |> frame Page.Register
        |> Html.map RegisterMsg 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个新的请求来指向创建用户 API 端点

```
-- src/Session/Request.elm
module Session.Request exposing (login, register)

-- ..

register : { r | name : String, email : String, password : String } -> Http.Request Session
register { name, email, password } =
    let
        user =
            Encode.object
                [ "name" => Encode.string name
                , "email" => Encode.string email
                , "password" => Encode.string password
                ]

        body =
            user
                |> Http.jsonBody
    in
        decodeSessionResponse
            |> Http.post (apiUrl "/users") body 
```

Enter fullscreen mode Exit fullscreen mode

最后整个寄存器. elm 就是这个

```
-- src/Session/Register.elm
module Session.Register exposing (ExternalMsg(..), Model, Msg, initialModel, update, view)

import Helpers.Decode exposing (optionalError, optionalFieldError)
import Helpers.Form as Form
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (..)
import Http
import Json.Decode as Decode exposing (Decoder, decodeString, field, string)
import Json.Decode.Pipeline exposing (decode)
import Route exposing (Route)
import Session.Model exposing (Session, storeSession)
import Session.Request exposing (register)
import Util exposing ((=>))
import Validate exposing (Validator, ifBlank, validate)

-- MESSAGES --

type Msg
    = SubmitForm
    | SetName String
    | SetEmail String
    | SetPassword String
    | RegisterCompleted (Result Http.Error Session)

type ExternalMsg
    = NoOp
    | SetSession Session

-- MODEL --

type alias Model =
    { errors : List Error
    , name : String
    , email : String
    , password : String
    }

initialModel : Model
initialModel =
    { errors = []
    , name = ""
    , email = ""
    , password = ""
    }

-- VIEW --

view : Model -> Html Msg
view model =
    div [ class "mt4 mt6-l pa4" ]
        [ h1 [] [ text "Sign up" ]
        , p [ class "f7" ]
            [ a [ Route.href Route.Login ]
                [ text "Have an account?" ]
            ]
        , div [ class "measure center" ]
            [ Form.viewErrors model.errors
            , viewForm
            ]
        ]

viewForm : Html Msg
viewForm =
    Html.form [ onSubmit SubmitForm ]
        [ Form.input "Name" [ onInput SetName ] []
        , Form.input "Email" [ onInput SetEmail ] []
        , Form.password "Password" [ onInput SetPassword ] []
        , button [ class "b ph3 pv2 input-reset ba b--black bg-transparent grow pointer f6" ] [ text "Sign up" ]
        ]

-- UPDATE --

update : Msg -> Model -> ( ( Model, Cmd Msg ), ExternalMsg )
update msg model =
    case msg of
        SubmitForm ->
            case validate modelValidator model of
                [] ->
                    { model | errors = [] }
                        => Http.send RegisterCompleted (register model)
                        => NoOp

                errors ->
                    { model | errors = errors }
                        => Cmd.none
                        => NoOp

        SetName name ->
            { model | name = name }
                => Cmd.none
                => NoOp

        SetEmail email ->
            { model | email = email }
                => Cmd.none
                => NoOp

        SetPassword password ->
            { model | password = password }
                => Cmd.none
                => NoOp

        RegisterCompleted (Err error) ->
            let
                errorMessages =
                    case error of
                        Http.BadStatus response ->
                            response.body
                                |> decodeString (field "errors" errorsDecoder)
                                |> Result.withDefault []

                        _ ->
                            [ "Unable to process registration" ]
            in
                { model | errors = List.map (\errorMessage -> Form => errorMessage) errorMessages }
                    => Cmd.none
                    => NoOp

        RegisterCompleted (Ok session) ->
            model
                => Route.modifyUrl Route.Home
                => SetSession session

-- VALIDATION --

type Field
    = Form
    | Name
    | Email
    | Password

type alias Error =
    ( Field, String )

modelValidator : Validator Error Model
modelValidator =
    Validate.all
        [ ifBlank .name (Name => "name can't be blank.")
        , ifBlank .email (Email => "email can't be blank.")
        , ifBlank .password (Password => "password can't be blank.")
        ]

-- DECODERS --

errorsDecoder : Decoder (List String)
errorsDecoder =
    decode (\name email password error -> error :: List.concat [ name, email, password ])
        |> optionalFieldError "name"
        |> optionalFieldError "email"
        |> optionalFieldError "password"
        |> optionalError "error" 
```

Enter fullscreen mode Exit fullscreen mode

我们已经完成了注册页面。转到浏览器并导航到注册菜单，您会看到类似这样的内容:

[![Toltec Register page with name, email and password fields](img/5b7f413efd3cf80f4d60ce29df1c2d9d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hjGwe3nM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/df592x577gjfsywd99os.png)

如果你输入一个新用户的信息，它将在后端应用程序中创建，你也应该登录

[![Toltec Home page after user creation and automatic loggin in](img/f5c9db7a40ce6042a0901c54f73c988e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aLd0-0-e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3fyz88vcezsg4i7mx6gd.png)

你可以在这里找到后端修改的源代码。前端的变化是[这里](https://github.com/miguelcoba/toltec-web/tree/part-04)。在这两种情况下，都要寻找 part-04 分支。

让我们暂时把它包在这里。我们已经在 Elm 应用程序中添加了登录和注册页面。

在第 5 部分中，我们将把会话存储在本地存储中，并添加一些视觉上的改进。