# 用榆树寻找真相

> 原文：<https://dev.to/1hko/finding-truth-with-elm-7on>

当使用[命题逻辑](https://en.wikipedia.org/wiki/Propositional_calculus)时，需要创建[真值表](https://en.wikipedia.org/wiki/Truth_table)，如下图所示

| **P** | **问** | **P** | **P ∨ Q** | **P ⇒ Q** |
| --- | --- | --- | --- | --- |
| T | T | F | T | T |
| T | F | F | F | F |
| F | T | T | T | T |
| F | F | T | T | T |

> ***表 1***
> 
> *   *`P ⇒ Q`在逻辑上等同于`¬P ∨ Q`*

做这个练习很重要，但也很容易出错。如果我们有一个程序为我们生成真值表，我们可以更确定结果。这让懒惰成为可能，所以我们当然想这么做。

### 目标

程序应该是直观的，简单地接受一个命题表达式列表。对表达式的分析将决定构建哪些`True` / `False`排列。例如，下面这组表达式包含 3 个唯一变量`P`、`Q`和`R`

| **P** | **问** | **R** | **(P ⇒ Q) ⇒ R** | **P ⇒ (Q ⇒ R)** | **(P ⇒ R) ∨ (Q ⇒ R)** |
| --- | --- | --- | --- | --- | --- |
| T | T | T | T | T | T |
| T | T | F | F | F | F |
| T | F | T | T | T | T |
| T | F | F | T | T | T |
| F | T | T | T | T | T |
| F | T | F | F | T | T |
| F | F | T | T | T | T |
| F | F | F | F | T | T |

> ***表二***
> 
> *   *`⇒`不可交换*
> *   *`(P ⇒ Q) ⇒ R`不等同于`P ⇒ (Q ⇒ R)`*
> *   *`P ⇒ (Q ⇒ R)`在逻辑上等同于`(P ⇒ R) ∨ (Q ⇒ R)`*

* * *

### 现已作为一个包提供

这个程序现在存在于 Elm 包库中， [`1hko/elm-truth-table`](http://package.elm-lang.org/packages/1hko/elm-truth-table/latest) 。你在这篇文章中看到的代码大部分是相同的，只是有一些东西被重命名了。

* * *

### 可能的 API

我不是在找什么花哨的东西。命题应该易于指定，允许用户在一组表达式中指定任意数量的变量—

```
type Expr =
    -- ...

truthTable : List Expr -> Html msg
truthTable exprs =
    -- ...

-- table 1
truthTable
    [ Not (Var "P")                  -- ¬P
    , Or (Not (Var "P")) (Var "Q")   -- ¬P ∨ Q
    , Implies (Var "P")  (Var "Q")   -- P ⇒ Q
    ]

-- table 2
truthTable
    -- (P => Q) => R
    [ Implies (Implies (Var "P")  (Var "Q")) (Var "R")

    -- P => (Q => R)
    , Implies (Var "P") (Implies (Var "Q")  (Var "R"))

    -- (P ⇒ R) ∨ (Q ⇒ R)
    , Or (Implies (Var "P") (Var "R")) (Implies (Var "Q") (Var "R"))
    ] 
```

Enter fullscreen mode Exit fullscreen mode

稍后我们可以创建一个将`"Q => P" : String`解析为`Implies (Var "Q") (Var "P") : Expr`的`String -> Expr`函数。但是现在，这是我们开始的全部需要。

* * *

### 实现

单个联合类型`Expr`足以满足我们程序的需求。我将在这里展示完整的类型，但是我知道在进入下一个
之前，我已经完全实现了一个变体

```
type Expr
    = Or Expr Expr
    | And Expr Expr
    | Xor Expr Expr
    | Implies Expr Expr
    | Equiv Expr Expr
    | Not Expr
    | Var String 
```

Enter fullscreen mode Exit fullscreen mode

#### 表头

每张桌子都以`<thead>`开头。我们的程序需要根据输入表达式来分析和创建列标题。在*表 1* 中，我们看到—

| **P** | **问** | **P** | **P ∨ Q** | **P ⇒ Q** |
| --- | --- | --- | --- | --- |
| … | … | … | … | … |

> ***表 1***
> 
> *   *表达式集中出现的每个变量一个列标题；`P`和`Q`*
> *   *每个表达式对应一列—`¬P`、`¬P ∨ Q`、`P ⇒ Q`、*

为了计算每个变量的列，我们创建了`vars` —

```
vars : Expr -> Set String
vars expr =
    case expr of
        Or a b ->
            Set.union (vars a) (vars b)

        Xor a b ->
            Set.union (vars a) (vars b)

        And a b ->
            Set.union (vars a) (vars b)

        Implies a b ->
            Set.union (vars a) (vars b)

        Equiv a b ->
            Set.union (vars a) (vars b)

        Not a ->
            vars a

        Var a ->
            Set.singleton a

And (Var "P") (Var "Q") |> vars
-- [ "P", "Q" ] : Set String 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将每个表达式转换成一个字符串，用于剩余的列标题—

```
exprToString : Expr -> String
exprToString expr =
    case expr of
        And a b ->
            exprToString a ++ " ∧  " ++ exprToString b

        Or a b ->
            exprToString a ++ " ∨ " ++ exprToString b

        Xor a b ->
            exprToString a ++ " ⊕ " ++ exprToString b

        Implies a b ->
            exprToString a ++ " ⇒ " ++ exprToString b

        Equiv a b ->
            exprToString a ++ " ⇔ " ++ exprToString b

        Not a ->
            "¬" ++ exprToString a

        Var a ->
            a

Implies (Var "P") (Var "Q") |> exprToString
-- "P ⇒ Q" : String 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以开始为`truthTable`—
填充一些实现

```
truthTable : List Expr -> Html msg
truthTable exprs =
    let
        ids =
            exprs
                |> List.map vars
                |> List.foldl Set.union Set.empty
                |> Set.toList

        columns =
            ids ++ List.map exprToString exprs

        -- ...
    in
    Html.table [ Attributes.style [ ( "width", "100%" ) ] ]
        [ Html.thead [] [ tableHeader columns ]
        , -- ...
        ]

tableHeader : List String -> Html msg
tableHeader labels =
    labels
        |> List.map (tableCell [ ( "font-weight", "bold" ) ])
        |> Html.tr []

tableCell : List ( String, String ) -> String -> Html msg
tableCell style text =
    Html.td [ Attributes.style style ] [ Html.text text ] 
```

Enter fullscreen mode Exit fullscreen mode

#### 表格行

继续前进，我们到达`<tbody>`。在*表 1* 中，我们看到需要为`P`和`Q`的每个可能场景生成`True` / `False`值

| **P** | **问** | **P** | **P ∨ Q** | **P ⇒ Q** |
| --- | --- | --- | --- | --- |
| T | T | … | … | … |
| T | F | … | … | … |
| F | T | … | … | … |
| F | F | … | … | … |

> ***表 1***
> *所有可能的`True` / `False`排列为 2 个变量，`P`和`Q`*

当这组表达式扩展到包括第三个变量`R`时，在*表 2* 中，增加了另一列，更多的`True` / `False`值也进行了排列

| **P** | **问** | **R** | **(P ⇒ Q) ⇒ R** | **P ⇒ (Q ⇒ R)** | **(P ⇒ R) ∨ (Q ⇒ R)** |
| --- | --- | --- | --- | --- | --- |
| T | T | T | … | … | … |
| T | T | F | … | … | … |
| T | F | T | … | … | … |
| T | F | F | … | … | … |
| F | T | T | … | … | … |
| F | T | F | … | … | … |
| F | F | T | … | … | … |
| F | F | F | … | … | … |

> ***表 2***
> *所有可能的`True` / `False`排列为 3 个变量，`P`、`Q`和`R`、*

我们实现`permute`来生成每行需要填充的值—

```
permute : Int -> List a -> List (List a)
permute n list =
    let
        loop acc n xs =
            if n == 0 then
                [ acc ]
            else
                list
                    |> List.concatMap (\x -> loop (acc ++ [ x ]) (n - 1) xs)
    in
    loop [] n list 

permute 2 [ "A", "B" ]
-- [ [ "A", "A" ]
-- , [ "A", "B" ]
-- , [ "B", "A" ]
-- , [ "B", "B" ]
-- ] : List (List String) 
```

Enter fullscreen mode Exit fullscreen mode

给定一个变量列表和一个值列表，我们可以计算一个表达式—

```
makeEnv : List String -> List Bool -> Env
makeEnv ids values =
    values
        |> List.map2 (,) ids
        |> Dict.fromList

eval : Env -> Expr -> Result String Bool
eval env expr =
    case expr of
        And a b ->
            Result.map2 (&&) (eval env a) (eval env b)

        Or a b ->
            Result.map2 (||) (eval env a) (eval env b)

        Xor a b ->
            Result.map2 xor (eval env a) (eval env b)

        Implies a b ->
            Result.map2 implies (eval env a) (eval env b)

        Equiv a b ->
            Result.map2 equiv (eval env a) (eval env b)

        Not a ->
            Result.map not (eval env a)

        Var a ->
            Dict.get a env
                |> Result.fromMaybe ("unbound identifier: " ++ toString a)

And (Var "P") (Var "Q")
    |> eval (makeEnv [ "P", "Q" ], [ True, False ])
    -- Ok False : Result String Bool 
```

Enter fullscreen mode Exit fullscreen mode

为了完成`truthTable`，我们首先生成`True` / `False`值排列，然后对每个表达式进行求值—

```
truthTable : List Expr -> Html msg
truthTable exprs =
    let
        ids =
            -- ...

        columns =
            -- ...

        count =
            List.length ids

        rows =
            permute count [ True, False ]
                |> List.map (tableRows (makeEnv ids) exprs)
    in
    Html.table [ Attributes.style [ ( "width", "100%" ) ] ]
        [ Html.thead [] [ tableHeader columns ]
        , Html.tbody [] rows
        ] 
```

Enter fullscreen mode Exit fullscreen mode

最后我们写`tableRows`把所有的部分粘在一起—

```
tableRows : (List Bool -> Env) -> List Expr -> List Bool -> Html msg
tableRows makeEnv exprs values =
    let
        env =
            makeEnv values

        results =
            exprs
                |> List.map (eval env)
                |> List.foldr (Result.map2 (::)) (Ok [])
                |> Result.map ((++) values)
                |> Result.withDefault []

        viewCell truth =
            if truth then
                tableCell [ ( "color", "#19A974" ) ] "T"
            else
                tableCell [ ( "color", "#E7040F" ) ] "F"
    in
    results
        |> List.map viewCell
        |> Html.tr [] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 实际应用

如果用户已登录，令牌有效，用户属于编辑者，帖子可编辑，帖子未锁定，则...–编写易于阅读的程序很困难。有时候复杂的逻辑命题可以用简化的[等价表达式](https://en.wikipedia.org/wiki/Logical_equivalence)来代替。真值表帮助我们识别这些等价

| **P** | **问** | 就这样产生了一个 1231 分钟的说法 | **(P ∨ Q)** |
| --- | --- | --- | --- |
| T | T | F | F |
| T | F | F | F |
| F | T | F | F |
| F | F | T | T |

> ***表 3***
> 
> ```
>  truthTable
>       [ And (Not (Var "P")) (Not (Var "Q"))
>       , Not (Or (Var "P") (Var "Q"))
>       ] 
> ```
> 
> *   *[德摩根定律](https://en.wikipedia.org/wiki/De_Morgan%27s_laws) : `¬(P ∨ Q)`相当于`(¬P) ∧ (¬Q)`*
> *   *即，“<u>非</u>多云<u>或</u>下雨”相当于“<u>非</u>多云<u>非</u>下雨”*

* * *

### 真值表生成器

通过 [ellie-app](https://ellie-app.com/Qs4HCPzYfra1) 上提供的完整源代码，自己寻找真相。

为了写这篇文章，我使用了这个在线工具[将 HTML 表格转换成 GHF Markdown](https://jakebathman.github.io/Markdown-Table-Generator/)

#### 待办事项

在时间允许的情况下，我将继续对此进行改进

*   `exprToString`应该包括圆括号`()`来消除像`P ⇒ Q ⇒ R`这样的表达的歧义
*   `Expr`联合类型大概可以简化为`type Expr = Unary ... | Binary ...`
*   一个解析器
*   你的想法🔥