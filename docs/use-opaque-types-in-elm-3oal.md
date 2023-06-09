# 在 Elm 中使用不透明类型！

> 原文：<https://dev.to/hecrj/use-opaque-types-in-elm-3oal>

加入[榆树懈](https://elmlang.herokuapp.com/)已经有一段时间了。在这段时间里，我看到不同的人询问如何以及何时使用**不透明类型**。我也见过许多不透明类型不存在的代码库和例子。

[![Strings, strings everywhere](img/84d85d48ec65595bbbf4a975ec9d8091.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5t44nDQZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2ks3pk.jpg)

我认为在 Elm 的 API 设计中，不透明类型是非常重要的，我怀疑它们的使用远远不够。在这篇文章中，我们将学习不透明类型如何将 API 粘合在一起，同时使我们的代码自文档化和无 bug 化。

# 常见场景

我们将使用一个我认为相当常见的场景:一个验证并提交一些数据以远程创建新资源的表单。这种交互是许多交互式 web 应用程序的核心。

为了简单起见，我们假设我们想要创建的资源是一个非常简单的**问题**，带有标题和正文。

首先，我们将从不使用任何不透明类型的代码开始。然后，我们将逐步添加它们，并分析代码是如何改进的！

我们开始吧！

# 用原语举例

我们想创建一个**问题**。假设我们用这个 API
编写了一个模块`Question`

```
module Question exposing (create)

create : String -> String -> Task Http.Error String 
```

这个`create`函数接受一个问题`title`和一个`body`，并返回一个试图创建适当问题的`Task`。这个`Task`要么失败返回一个`Http.Error`，要么成功返回一个代表全新问题`slug`的`String`。

然后，我们构建一个表单，以便我们的用户可以创建问题。我们将关注提交表单的代码，忽略一些不相关的细节，比如字段是如何更新/呈现的:

```
type alias Model =
    { title : String
    , body : String
--  , ...
    }

type Msg
    = Submit
    | QuestionCreated (Result Http.Error String)
--  | ...

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        Submit ->
            ( model
            , Question.create model.title model.body
                |> Task.attempt QuestionCreated
            )

        -- ...

view : Model -> Html Msg
view model =
    form
      [ onSubmit Submit ]
      [ titleField model
      , bodyField model
      , submitButton model
      ] 
```

你注意到这里有什么问题吗？让我们看看。当我们改变我们的形式时会发生什么？我们使用`Question.create`创建问题。听起来不错！但是等等...验证在哪里？！如果`model.title`是空的会怎么样？如果`model.body`特别长怎么办？嗯（表示踌躇等）...好的，没问题。我们只需要加上一个如果-那么-否则，对吗？

```
case msg of
    Submit ->
        if Question.titleIsValid model.title && Question.bodyIsValid model.body then
            ( model
            , Question.create model.title model.body
                |> Task.attempt QuestionCreated
            )
        else
            ( model, Cmd.none )

    -- ... 
```

酷！我们到此为止。新的`titleIsValid`和`bodyIsValid`函数处理验证，现在无法创建无效问题。

不过，这是不是感觉有点扯了？每当我们想要使用`create`时，我们必须**记住**来通过使用`titleIsValid`和`bodyIsValid`来验证它的输入。如果有些需求改变了，`create`需要另一个论证怎么办？我们将不得不**记住**来更新 if-then-else。如果某个新开发人员阅读了`create`的类型签名，并决定在没有任何验证的情况下在其他地方使用它，该怎么办？不可取。

最后，我们的 API 容易出错。**可以用错**。我们能做得更好吗？如果我们能以某种方式将验证和创建问题的概念联系在一起，那不是很好吗？

# 伸手不见五指的救援类型！

在我们继续之前，让我们回顾一下什么是不透明类型:

> 不透明类型是没有公开构造函数的自定义类型。

好吧，但是如果构造函数没有公开...我们如何使用不透明类型？构造函数是可以在内部访问的，在定义了自定义类型的模块中。因此，我们可以公开函数来*控制*如何创建不透明类型的值。

让我们看看这是如何工作的！回到我们的`Question`模块:

```
module Question exposing (create)

create : String -> String -> Task Http.Error String 
```

`create`的问题是它的参数类型接受无效的域值。例如，`""`是一个完全有效的`String`，但它不是一个有效的问题标题。

我们能否创建一个新的自定义类型，它的可能值总是有效的问题标题？我们来看看:

```
module Question exposing (Title, create, titleFromString)

type Title
    = Title String

titleFromString : String -> Result String Title
titleFromString title =
    if String.length title < 5 then
        Err "the title must not be less than 5 characters long"
    else if String.length title > 100 then
        Err "the title must not be more than 100 characters long"
    else
        Ok (Title title)

-- ... 
```

就是这样！这里我们定义了一个新的自定义类型`Title`，而没有公开它的构造函数。相反，我们实现了一个`titleFromString`函数，它接受一个`String`，并在验证失败时返回一个描述错误的`String`，或者在验证成功时返回一个`Title`。

构建`Title`的唯一方法是使用`titleFromString`。因此，如果我们的代码库中有一个`Title`，我们可以确信它是一个有效的问题标题。`Title`型**保证有效性**。

每当我们验证数据时，我们只是检查数据是否有一些**保证**。执行验证，然后使用相同的类型是一个错失的机会！我们应该尝试使用不透明类型来获取这些保证。因此，我们的 API 将变得更加安全和易于理解。

类似地，我们可以定义一个`Body`类型。然后，我们可以更新`create`函数:

```
module Question exposing (Body, Title, create, bodyFromString, titleFromString)

-- type Title ...
-- type Body ...

create : Title -> Body -> Task Http.Error String 
```

现在，`create`函数迫使我们提供一个有效的标题和一个有效的主体。我们不能再像以前那样提交表格了。我们必须使用`titleFromString`和`bodyFromString` :

```
case msg of
    Submit ->
        case ( Question.titleFromString model.title, Question.bodyFromString model.body ) of
            ( Ok title, Ok body ) ->
                -- Validation succeded
                ( model
                , Question.create title body
                    |> Task.attempt QuestionCreated
                )

            _ ->
                -- Validation failed
                ( model, Cmd.none )

    -- ... 
```

我们的 API 没有提供任何其他方法来做到这一点。我们不能再跳过验证了！我们甚至不需要**记住**验证。API **迫使**我们在这个过程中处理错误。我们的 API 是**更安全**。

# 进一步改进

我们还没完呢！我们仍有一些地方可以改进。

第一个改进与**重复验证**有关。当表单被提交时，我们目前正在我们的`update`函数中执行验证，但是我们可能也想在我们的`view`代码中验证表单字段，可能会实时显示错误消息。因此，`update`和`view`都在使用`titleFromString`和`bodyFromString` : `update`只关心成功的结果，而`view`只关心错误。

现在我们已经有了`Title`和`Body`类型，我们可以让我们的`Submit`消息**在表单值无效**的情况下无法被触发，只在`view`中验证一次，并将**验证保证**传播到`update`。我们只需要改变`Submit`消息:

```
type Msg
    = Submit Question.Title Question.Body
    -- |...

case msg of
    Submit title body ->
        ( model
        , Question.create title body
            |> Task.attempt QuestionCreated
        )

    -- ... 
```

干净！当验证失败时，我们去掉了 else 子句中令人尴尬的空操作。

这种特殊的方法，即表单验证在`view`中发生，同时使用不透明类型获取担保，是我编写的包 [`composable-form`](https://github.com/hecrj/composable-form) 背后的主要思想之一。 [`composable-form`](https://github.com/hecrj/composable-form) 将表单视为可组合的单元，因此可以自由地构建、组合和重用它们。我很快会写出来的！

第二个改进很简单。我们可以创建一个`Slug`不透明类型。然后，`create`函数看起来会更好:

```
create : Title -> Body -> Task Http.Error Slug 
```

现在，如果我们需要允许我们的用户编辑问题，我们可以只写一个`edit`函数:

```
edit : Slug -> Title -> Body -> Task Http.Error Slug 
```

整洁！我们的 API 将明确指出，一个问题只有在之前被创建过的情况下才能被编辑。

# 总结

不透明类型不仅使你的代码更安全，而且它们还可以用来将不同的概念联系在一起，使你的代码库更容易理解。

这里有一个我在编码时喜欢做的练习:我试着想象一个有着明确目标的新开发人员在阅读模块文档时的思维过程。让我们用我们的`Question`模块来做这件事，假设 Bob 想知道如何`create`一个新问题:

1.  鲍勃找到了一个`create`函数。
2.  鲍勃认为如果他提供一个`Title`和一个`Body`，他就可以`create`一个问题。
3.  Bob 想知道是否有某种方法可以使用`String`来构建这些。
4.  鲍勃找到了`titleFromString`和`bodyFromString`，它们似乎可以完成这项工作。
5.  Bob 注意到这些函数返回了一个`Result`。
6.  Bob 知道他需要处理提供的`String`无效的情况。

在这种情况下，开发人员可以通过查看类型签名来清楚地理解`Question` API。不需要评论！

* * *

现在就这样了！我很快会写更多关于榆树的东西。

我想听听你对不透明类型和表单验证的想法，所以请不要犹豫发表评论！你也可以在[榆树懈](https://elmlang.herokuapp.com/)里找到我( [@hecrj](https://dev.to/hecrj) )，我总是乐于交谈。