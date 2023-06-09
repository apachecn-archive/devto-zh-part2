# 视图的类型

> 原文：<https://dev.to/noredink/a-type-for-views-5g04>

这篇文章最初出现在诺丁克的博客上。

编写 Elm 应用程序的乐趣之一是编译器捕捉我们的错误的许多方式，以及当它这样做时给我们的有用的错误消息。不过这是一条双行道，意味着编译器剔除错误的程度取决于我们向它披露了什么信息。换句话说，我们必须帮助编译器自助。在这篇文章中，我们将看到这对于视图函数来说比其他任何地方都更真实，我们将看到如何帮助编译器帮助我们得到正确的视图函数。

## 一个图书推荐应用

假设我们正在构建一个网页，按照受欢迎程度排序，显示您最喜欢的作者的最新新书。该应用程序的主页是一个简单的图书列表，列出了每本书的标题、作者姓名和 ISBN。让我们为这个应用程序创建一个模型。

```
type alias Model =
    { authors : List Author
    }

type alias Author =
    { name : String
    , books : List Book
    , favorite : Bool
    }

type alias Book =
    { title : String
    , isbn : String
    , popularity : Int
    } 
```

Enter fullscreen mode Exit fullscreen mode

接下来是这个应用程序的视图功能。对于这个视图，我们需要对模型中的数据进行一些转换。首先，我们对显示用户不喜欢的作者的书不感兴趣，所以我们可以把它们过滤掉。其次，我们想按受欢迎程度对其余作者的书进行排序。第三，我们希望在页面上显示包含来自`Book` (title，isbn)和`Author` (name)类型的数据的项目。

## 间奏曲:我们确定要这么做吗？

但是等等，也许不写一个转换函数，更简单的方法是改变我们的`Model`类型，使之更接近页面的结构！例如，假设我们的模型是这样的:

```
type alias Model =
    { books : List Book
    }

type alias Book =
    { authorName : String
    , authorIsFavorite : Bool
    , title : String
    , isbn : String
    , popularity : Int
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的视图函数将更容易编写。但是这种模式有一个缺点。同一作者的两本书可能在该作者是否是用户的最爱之一上有分歧。以前的模型防止了这种不可能的状态，因为作者的`favorite`状态只存储在一个地方。当面临这种权衡时，我们更喜欢我们的模型尽可能地防止许多不可能的状态，即使我们的视图函数需要做一些转换。

## 查看 NoRedInk 的转换

我们在 NoRedInk 开发的许多 Elm 应用程序都有一个模型，看起来与我们在屏幕上绘制的 Html 非常不同。因此，我们的视图函数将需要转换数据，但这不是问题，尤其是如果编译器有助于确保我们正在正确地转换。但是有一个问题:我们需要做一些工作来让编译器帮助我们。

为了理解为什么，让我们看看每个 Elm 应用程序中都存在的转换:将旧模型状态转换为新模型状态的更新功能。编译器在这一点上帮了我们很大的忙，它确保了无论发生什么，我们的更新函数将总是返回一个有效的新模型，这防止了各种各样的错误。但是对于视图函数，我们没有这样的保证，编译器只确保它返回 Html。如果一切顺利，Html 将看起来像我们的规范，但就编译器而言，它也可能是一只猫的图像。两者都是 Html，所以调用我们的视图函数同样有效。

为了防止这样的错误，我们对视图函数的有效输出进行了一些额外的限制。我们可以通过定义一个描述页面形状的新类型来做到这一点。然后，我们将视图功能分成两部分。首先，我们将我们的模型转换成这个`View`类型，这是困难的部分。然后我们把这个类型转换成 Html。因为该类型被设计成类似于页面的结构，所以第二部分应该很容易。

## 我们来改造一些书吧

让我们回到我们的图书推荐 app，为它写一个`View`类型。

```
type alias View =
    List BookView

type alias BookView =
    { authorName : String
    , title : String
    , isbn : String
    , popularity : Int
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以编写将我们的`Model`转换成`View`类型的转换函数。这是工作中最难的部分，但是因为转换发生在两个特殊类型`Model`和`View`之间，我们将从 Elm 编译器中获得很多帮助。

```
toView : Model -> View
toView model =
    model.authors
        |> List.filter .favorite
        |> List.concatMap booksForAuthor

booksForAuthor : Author -> List BookView
booksForAuthor author =
    author.books
        |> List.map
            (\{ title, isbn, popularity } ->
                { authorName = author.name
                , title = title
                , isbn = isbn
                , popularity = popularity
                }
            )
        |> List.sortBy .popularity 
```

Enter fullscreen mode Exit fullscreen mode

最后，剩下的就是编写视图函数，将我们的`View`类型呈现给`Html`。

```
view : Model -> Html msg
view model =
    toHtml (toView model)

toHtml : View -> Html msg
toHtml books =
    Html.ul [] (List.map viewBook books)

viewBook : BookView -> Html msg
viewBook book =
    Html.li []
        [ Html.text ("Author: " ++ book.authorName)
        , Html.text ("Title: " ++ book.title)
        , Html.text ("ISBN: " ++ book.isbn)
        ] 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们已经看到了如何利用编译器提供的一些额外帮助，使用一个`View`类型来编写我们的视图函数。

作为一个好的副作用，这种方法也使得单元测试我们的视图逻辑变得容易。我们可以为我们的`toView`函数编写普通的 Elm 单元测试，它包含了我们所有的实际转换逻辑。这允许我们针对我们的`Html`返回视图函数编写更少的测试，尽管这可能更难。

[![](img/5662b11c39eb16fb5e2d0bdaca04c38c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---UZX81fh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/jiCpoq9.png) 
**施特费尔**
[@施特费尔](https://twitter.com/schtoeffel)
工程师 at [诺丁克](http://noredink.com)

[![](img/2ec1e0953660277b54efed4ed49021a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uFwOkm4a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/J491Oom.png) 
**贾斯珀·沃登伯格**
[@贾斯珀·沃登伯格](https://twitter.com/jasperwoudnberg)
工程师在[诺丁克](http://noredink.com)