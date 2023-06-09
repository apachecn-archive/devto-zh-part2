# Elm 中带浮点的受控数字输入

> 原文：<https://dev.to/joanllenas/controlled-number-input-with-floats-in-elm-4jin>

术语*受控*取自 [React 文档](https://reactjs.org/docs/forms.html#controlled-components)，但它也适用于 Elm。
在表单的上下文中, *controlled* 意味着模型管理输入字段的值，因此，当我们键入一些内容时，更改会传播到模型，并且在此之后，输入值也会立即更新。

# 问题

当受控输入保存的值是一个字符串时，它们工作得很好，但是当值是一个数字时，我们就不能这样说了。

### 做作的例子

([艾莉链接](https://ellie-app.com/NXLv3NJZWma1) )

```
module Main exposing (..)

import Html exposing (..)
import Html.Attributes as Attrs exposing (..)
import Html.Events exposing (..)

type Msg
    = SetPrice String

type alias Model =
    { price : Float }

model : Model
model =
    { price = 0 }

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        SetPrice price ->
            ( { model | price = price |> String.toFloat |> Result.withDefault 0 }
            , Cmd.none 
            )

view : Model -> Html Msg
view model =
    div []
        [ Html.form [] 
            [ label [] [text "Price"]
            , input [placeholder "Price", value (toString model.price), onInput SetPrice ] []
            , br [] []
            , p [] [ text ("Price is: " ++ (toString model.price)) ]
            ]
        ]

main : Program Never Model Msg
main =
    Html.program
        { init = ( model, Cmd.none )
        , update = update
        , subscriptions = \_ -> Sub.none
        , view = view
        } 
```

Enter fullscreen mode Exit fullscreen mode

这里的主要问题是,`string -> float`转换只允许有效的数字，因此任何创建无效数字的中间步骤都会被转换为`0`。

### 这种方法的问题

*   你不能有一个空的输入字段，它总是显示`0`。
*   当您错误地键入非数字字符时，整个数字将被删除。
*   您不能输入小数，因为小数分隔符`.`在`string -> float`转换后被去除。
*   您不能键入负数，因为负号`-`在`string -> float`转换后会被去除。

# 研究

我已经找到了一些解决这个问题的方法:

*   Html 数字表单输入([https://discourse . elm-lang . org/t/html-Number-Form-Inputs/740](https://discourse.elm-lang.org/t/html-number-form-inputs/740))
*   解决 Elm 指南年龄练习的各种解决方案([http://www . Bravo-kernel . com/2016/06/variable-solutions-for-solutions-the-Elm-Guide-age-exercise/](http://www.bravo-kernel.com/2016/06/various-solutions-for-solving-the-elm-guide-age-exercise/))
*   用`defaultValue`代替`value`。

但是他们都不能满足我的需求。

# 我的贡献

我的解决方案必须满足以下条件:

*   输入类型必须是`text`因为我要支持 iOS 和 Android，这两个系统的输入类型数字支持很差。
*   输入的记录值必须是一个`Float`，而不是一个`String`。
*   输入字段可能为空。
*   所有这些，并最终解决上述所有的打字问题。

([艾莉链接](https://ellie-app.com/P59jnfvv7wa1) )

```
module Main exposing (..)

import Html exposing (..)
import Html.Attributes as Attrs exposing (..)
import Html.Events exposing (..)

type Msg
    = SetPrice String

type PriceField
    = PriceField (Maybe Float) String

type alias Model =
    { price : PriceField }

model : Model
model =
    { price = PriceField Nothing "" }

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        SetPrice price ->
            if String.right 1 price == "." then
                ( { model | price = PriceField Nothing price }, Cmd.none )
            else
                let
                    maybePrice =
                        price |> String.toFloat |> Result.toMaybe
                in
                case maybePrice of
                    Nothing ->
                        ( { model | price = PriceField Nothing price }, Cmd.none )

                    Just p ->
                        ( { model | price = PriceField (Just p) price }, Cmd.none )

priceFieldToString : PriceField -> String
priceFieldToString priceField =
    case priceField of
        PriceField Nothing price ->
            price

        PriceField (Just price) _ ->
            toString price

view : Model -> Html Msg
view model =
    div []
        [ Html.form []
            [ label [] [ text "Price" ]
            , input [ placeholder "Price", value (priceFieldToString model.price), onInput SetPrice ] []
            , br [] []
            , small [] [ text ("Price is: " ++ toString model.price) ]
            ]
        ]

main : Program Never Model Msg
main =
    Html.program
        { init = ( model, Cmd.none )
        , update = update
        , subscriptions = \_ -> Sub.none
        , view = view
        } 
```

Enter fullscreen mode Exit fullscreen mode

## 重述

这里的关键是`PriceField`产品类型，它允许我们在同一个地方输入`String`值和`Maybe Float`。

我们还可以使用`Maybe Float`作为无效输入状态的指示器，因此我们可以添加定制的验证逻辑:

([艾莉·林克——同样的解决方案加上一些基本的验证](https://ellie-app.com/P8wPHrWZz8a1) )

```
-- (...)
priceValidationStyle : PriceField -> List ( String, String )
priceValidationStyle priceField =
    case priceField of
        PriceField Nothing price ->
            if price == "" then
                []
            else
                [ ( "background-color", "red" ) ]

        PriceField (Just price) _ ->
            []

view : Model -> Html Msg
view model =
    div []
        [ Html.form []
            [ label [] [ text "Price" ]
            , input [ placeholder "Price", value (priceFieldToString model.price), onInput SetPrice, style (priceValidationStyle model.price) ] []
            , br [] []
            , small [] [ text ("Price is: " ++ toString model.price) ]
            ]
        ]

-- (...) 
```

Enter fullscreen mode Exit fullscreen mode

我可以想象如何使用相同的策略向相同的类型添加更高级的验证信息，但这是我目前所知道的。

如果你知道的话，我很想听听还有什么其他的策略或方案可以用来实现类似的目标。