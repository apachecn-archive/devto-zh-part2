# 在 Elm 中模拟一个保存按钮

> 原文：<https://dev.to/jwoudenberg/modelling-a-save-button-in-elm-2eo8>

web 应用程序中一个常见的功能是保存按钮。我最近不得不为一个 Elm 应用程序实现了一个，并且对结果相当满意，所以我想在这篇文章中分享这个方法。

我相信无论我们试图拯救什么，无论是花园设计还是活页乐谱，这种方法都会奏效。在这篇文章中，我将使用一个博客应用程序作为例子，但实际上我们可以保存任何东西。

我们的博客应用程序将会非常简单:它只是一个带有保存按钮的文本区域。我们希望“保存”按钮以如下方式运行:

1.  如果存在任何未保存的更改，将出现“保存”按钮。
2.  点击“保存”按钮将发送一个包含文章当前内容的 HTTP 请求。
3.  当请求正在进行时,“保存”按钮应该用一个微调按钮替换。
4.  如果请求失败，应该显示一个警告。
5.  用户可以在保存文章的同时继续编辑文章。

首先让我们决定我们的博客文章的类型。正如我之前提到的，我们保存什么并不重要，所以为了这个例子的目的，让我们只取我们能想到的最简单的博客类型:单个字符串。

```
type Blog
    = Blog String 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看我们的第一个要求:如果有任何未保存的更改，显示一个保存按钮。

## 检测变化

我们需要区分已保存的帖子和包含未保存更改的帖子。一种方法是用构造函数为任一场景定义一个类型:

```
type MaybeSaved doc
    = Saved doc
    | HasUnsavedChanges doc doc 
```

Enter fullscreen mode Exit fullscreen mode

`HasUnsavedChanges`有两个构造函数，所以我们可以存储最后保存的文章和文章的当前版本。但是这种类型有一个问题:它允许一种不可能的状态:

```
blog : MaybeSaved Blog
blog =
    HasUnsavedChanges
        (Blog "# Bears")
        (Blog "# Bears") 
```

Enter fullscreen mode Exit fullscreen mode

`blog`是否包含变化？`HasUnsavedChanges`构造函数暗示它是这样的，但是 post 的最后修改版本和当前版本是相同的。如果检测到两个`docs`相同，我们可以编写逻辑自动将一个`HasUnsavedChanges doc doc`转换成一个`Saved doc`，但是如果类型一开始就不允许无效状态就更好了。

幸运的是，我们可以对我们的类型做一个简单的修正来消除这种不可能的状态:

```
type MaybeSaved doc
    = MaybeSaved doc doc

{-| Get the doc containing the latest changes.
-}
currentValue : MaybeSaved doc -> doc
currentValue (MaybeSaved _ current) =
    current

{-| Check if there are any unsaved changes.
-}
hasUnsavedChanges : MaybeSaved doc -> Bool
hasUnsavedChanges (MaybeSaved old new) =
    old /= new

{-| Update the current value but do not touch the saved value.
-}
change : (doc -> doc) -> MaybeSaved doc -> MaybeSaved doc
change changeFn (MaybeSaved lastSaved current) =
    MaybeSaved lastSaved (changeFn current)

{-| Call this after saving a doc to set the saved value.
-}
setSaved : doc -> MaybeSaved doc -> MaybeSaved doc
setSaved savedDoc (MaybeSaved _ current) =
    MaybeSaved savedDoc current 
```

Enter fullscreen mode Exit fullscreen mode

在这种类型中，我们总是存储帖子的两个版本，并使用一个函数来比较这两个版本。如果有差异，我们知道有未保存的更改，我们需要显示我们的“保存”按钮。

这种方法是 NoRedInk/elm 保存的库的基础。

酷，一个要求完成了，还有四个！让我们尝试实现保存请求。

## 点击‘保存’

当我们保存我们的文章时，我们将发出一个 HTTP 请求。我们可以在模型中使用一个单独的属性来跟踪这个请求:

```
type SaveRequest
      -- There's currently no save request underway
    = NotSaving
      -- A save request has been sent and we're waiting for the response
    | Saving
      -- A save request failed
    | SavingFailed Http.Error 
```

Enter fullscreen mode Exit fullscreen mode

你有没有注意到在这种类型中没有地方存放一个`blog`？不需要，因为存储博客是我们`MaybeSaved a`类型的责任。对于我们的`SaveRequest`类型，我们只要求它跟踪请求的状态，而不是结果。

如果你在我们的`SaveRequest`类型之前使用过 [krisajenkins/remotedata](http://package.elm-lang.org/packages/krisajenkins/remotedata/latest) 库，你可能会觉得很熟悉:这就像没有`Success`变体的`RemoteData e a`。我们并不真的需要那个`Success`变种来满足我们的要求，但是你的情况可能不一样。

## 把所有的东西放在一起

有了这两种类型，让我们构造我们的`Model`、`Msg`和`update`函数。

```
module BlogApp exposing (..)

import Blog exposing (Blog)
import Http
import Json.Decode
import MaybeSaved exposing (MaybeSaved)

type alias Model =
    { blog : MaybeSaved Blog
    , saveRequest : SaveRequest
    }

type Msg
    = MakeChange Blog
    | Save
    | ReceivedSaveResponse Blog (Result Http.Error ())

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        -- The user changes their post, for example by typing some words.
        MakeChange newBlog ->
            ( { model | blog = MaybeSaved.change (\_ -> newBlog) model.blog }
            , Cmd.none
            )

        -- The user presses 'save'.
        Save ->
            let
                blogToSave =
                    MaybeSaved.currentValue model.blog
            in
            ( { model | saveRequest = Saving }
            , Http.post "/my-blog"
                (Http.jsonBody (Blog.encode blogToSave))
                (Json.Decode.succeed ())
                |> Http.send (SaveResponse blogToSave)
            )

        -- We successfully saved the blog post!
        ReceivedSaveResponse savedBlog (Ok ()) ->
            ( { model
                | saveRequest = NotSaving
                , blog = MaybeSaved.setSaved savedBlog model.blog
              }
            , Cmd.none
            )

        -- We tried to save the blog post, but it failed :-(.
        ReceivedSaveResponse _ (Err e) ->
            ( { model | saveRequest = SavingFailed e }
            , Cmd.none
            ) 
```

Enter fullscreen mode Exit fullscreen mode

## 我们完了吗？

最后，让我们再看一遍我们的需求，看看我们做得如何。

> 如果存在任何未保存的更改，将出现“保存”按钮。

我们可以在我们的`view`函数中使用`MaybeSaved.hasUnsavedChanges`来检查更改，如果存在的话，就呈现一个按钮。

> 点击“保存”按钮将发送一个包含文章当前内容的 HTTP 请求。

检查！该按钮可以发送一条`Save`消息。我们在`update`函数中编写了发送请求的逻辑。

> 当请求正在进行时,“保存”按钮应该用一个微调按钮替换。

视图逻辑可以基于`saveRequest`属性。如果它的值为`Saving`，则请求正在进行中。

> 如果请求失败，应该显示一个警告。

视图逻辑可以基于`saveRequest`属性并检查错误。也许你会想添加一些逻辑来使错误在一段时间后消失，或者当被用户消除时。

> 用户可以在保存文章的同时继续编辑文章。

没错。保存功能不会妨碍继续编辑。
如果用户在保存请求待定时进行了更改，则在保存成功后，这些更改将被标记为“未保存的更改”。

很好，满分！仅此而已！

让我知道你对这种方法的看法，以及你知道的其他解决问题的方法。我很想听听他们的故事！