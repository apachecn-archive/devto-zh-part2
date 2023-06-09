# 从 elm-validate 转移到 elm-verify

> 原文：<https://dev.to/zaptic/moving-from-elm-validate-to-elm-verify--19pc>

在 [Zaptic](https://www.zaptic.com) ，我们使用 Elm 作为我们的商业管理网站，允许客户配置他们想要执行的流程。该网站包含许多不同的形式，用于在我们的系统中添加&编辑各种实体。所有这些表单都受益于数据提交到服务器之前的客户端验证。

当我们开始编写 Elm 代码时，推荐的表单验证方法是 [rtfeldman/elm-validate](https://github.com/rtfeldman/elm-validate) 库。对于由这样的数据结构支持的表单:

```
type alias ProductGroup =
    { name : String
    , mode : Maybe Mode
    , selectedProducts : Dict Int Product

    -- Used for storing validation errors
    , errors : List ( Field, String )
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们可能有这样一个验证函数:

```
validateProductGroup : Validator ( Field, String ) ProductGroup
validateProductGroup =
    Validate.all
        [ .name >> ifBlank ( NameField, "Please provide a name" )
        , .mode >> ifNothing ( ModeField, "Please select a mode" )
        , .selectedProducts >> ifEmptyDict ( ProductsField, "Please select at least one product" )
        ] 
```

Enter fullscreen mode Exit fullscreen mode

这将检查`name`字段是否为空，是否选择了`mode`以及是否选择了一些产品。结果是不符合标准的错误列表:

```
validateProductGroup { name = "", mode = Just MultiMode, selectedProducts = Dict.empty }
-- [(NameField, "Please provide a name"), (ProductsField, "Please select at least one product")] 
```

Enter fullscreen mode Exit fullscreen mode

列表可以保存在模型中，然后视图在显示各种输入字段时搜索列表中的相关条目，以便向用户显示错误状态(如果有的话)。

考虑到这一点，我们可以创建一个表单，在表单提交时发送一条`SaveProductGroup`消息，我们可以用
在我们的`update`函数中处理这条消息

```
 SaveProductGroup ->
            case validateProductGroup model.productGroupData of
                [] ->
                    { model | productGroupData = { productGroupData | errors = Nothing } }
                        ! [ postProductGroup model.productGroupData
                          ]

                results ->
                    { model | productGroupData = { productGroupData | errors = Just results } } ! [] 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们运行`validateProductGroup`函数，如果我们得到一个空列表，那么我们知道没有错误，我们可以使用`postProductGroup`将我们的数据发送到服务器，这将为我们创建一个命令来执行必要的 HTTP 请求。

我们遇到的问题是,`postProductGroup`需要将`ProductGroup`结构编码成 JSON，当它这样做时，它必须导航我们给它的数据。我们知道`mode`值是一个`Just`，因为我们已经对它进行了验证，但是 convert to JSON 函数不能走任何捷径，因为 Elm 不允许(这是一件好事！)我们可以试着把编码函数写成:

```
encodeProductGroup : ProductGroup -> Json.Encode.Value
encodeProductGroup { name, mode, selectedProducts } =
    Json.Encode.object
        [ ( "name", Json.Encode.string name )
        , ( "mode"
          , case mode of
                Just value ->
                    encodeMode value

                Nothing ->
                    -- Help! What do we put here?
          )
        , ( "products", Json.Encode.list <| List.map encodeProduct <| Dict.values selectedProducts )
        ] 
```

Enter fullscreen mode Exit fullscreen mode

从评论中可以看出，很难弄清楚在那里写什么。Elm 给了我们一些类似`Debug.crash`的逃生出口，但这感觉是错误的。如果没有值，我们就不能包含`mode`键，但是这样我们就故意允许代码路径向服务器发送不正确的数据。

那我们该怎么办？我们需要认识到，这个功能不应该做出这个决定。它不应该处理`Maybe`，尤其是当我们已经验证了它有一个`Just`值的时候。

所以我们创建了一个新的类型:

```
type alias ProductGroupUploadData =
    { name : String
    , mode : Mode
    , selectedProducts : List Product
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是我们感兴趣的相同数据，但是`Maybe`被解析为实际值，并且`Dict.values`转换已经应用于`selectedProducts`。如果我们改变我们的`encodeProductGroup`函数来期望这种类型，那么实现变得微不足道:

```
encodeProductGroup : ProductGroupUploadData -> Json.Encode.Value
encodeProductGroup { name, mode, selectedProducts } =
    Json.Encode.object
        [ ( "name", Json.Encode.string name )
        , ( "mode", encodeMode mode )
        , ( "products", Json.Encode.list <| List.map encodeProduct selectedProducts )
        ] 
```

Enter fullscreen mode Exit fullscreen mode

但是我们如何将我们的`ProductGroup`转换成`ProductGroupUploadData`？这就是 [stoeffel/elm-verify](https://github.com/stoeffel/elm-verify) 库的用武之地。它允许我们在同一个操作中验证数据并将其转换为另一种结构。它通过使用`Result`类型来允许它报告验证错误(如果遇到的话),或者新的数据结构供我们使用。它通过一个类似于`Json.Decode.Pipeline`的接口:
来做到这一点

```
validateProductGroup : ProductGroup -> Result (List ( Field, String )) ProductGroupUploadData
validateProductGroup =
    let
        validateProducts productGroup =
            if Dict.isEmpty productGroup.selectedProducts then
                Err [ ( ProductsField, "Please select at least one product" ) ]
            else
                Ok (Dict.values productGroup.selectedProducts)
    in
    V.validate ProductGroupUploadData
        |> V.verify .name (String.Verify.notBlank ( NameField, "Please provide a name" ))
        |> V.verify .mode (Maybe.Verify.isJust ( ConfigField, "Please select a mode" ))
        |> V.custom validateProducts 
```

Enter fullscreen mode Exit fullscreen mode

其中`V`是`import Verify as V`的结果。您可以从`Json.Decode.Pipeline`中看到熟悉的“管道”方法。这意味着我们使用`ProductGroupUploadData`作为构造函数，管道的每一步都提供一个参数来完成数据。我们使用`String.Verify`来检查`name`不为空，使用`Maybe.Verify`来检查`mode`是否被指定。然后我们使用`Verify.custom`为`selectedProducts`提供稍微复杂一点的检查。`Verify.custom`允许我们编写一个函数，该函数接收输入数据并返回一个 [`Result`](http://package.elm-lang.org/packages/elm-lang/core/latest/Result) ，返回一个带有错误数组的`Err`或一个带有有效值的`Ok`。我们不仅使用它来检查字典是否为空，还从字典中提取值。我们不必在这里运行`Dict.values`，我们也可以在生成 JSON 时在`encodeProductGroup`函数中这样做，但是我个人更喜欢`UploadData`尽可能地匹配 JSON。

有了这些，我们可以在我们的`update`函数中修改我们的`SaveProductGroup`案例，看起来像这样:

```
 SaveProductGroup ->
            case validateProductGroup model.productGroupData of
                Ok uploadData ->
                    { model | productGroupData = { productGroupData | errors = Nothing } }
                        ! [ postProductGroup uploadData
                          ]

                Err errors ->
                    { model | productGroupData = { productGroupData | errors = Just errors } } ! [] 
```

Enter fullscreen mode Exit fullscreen mode

这意味着`postProductGroup`获得了一个不错的`ProductGroupUploadData`记录，并且不再需要担心`Maybe`类型。

在使用`elm-verify`库之前，我们使用了一个单独的函数在类型之间进行转换，只有当两个验证&转换函数都成功时，我们才调用`postProductGroup`。转换功能总是感觉有点奇怪，切换到`elm-verify`很好地解决了这个问题。

**进一步注意**:虽然`elm-verify`界面与`Json.Decode.Pipeline`相似，但并不完全相同。它有一个`andThen`的版本，但是它没有提供一些方法来组合像`Json.Decode.oneOf`这样的操作或者像`Json.Decode.map`这样的辅助函数。这部分是为了保持界面简单，部分是因为它总是操纵`Result`的值，所以稍加思考，你就可以很容易地使用像`Result.map`这样的辅助函数。

我只是把它包括进来，因为最初我试图完全按照我会使用的方式使用`elm-verify`并最终写了一堆不必要的函数来允许我这样做。我应该花更多的时间去理解接口中的类型，并使用它们。