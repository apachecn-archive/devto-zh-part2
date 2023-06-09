# 康莫纳德素描~ 1\. DOM 拼贴画~

> 原文：<https://dev.to/e_ntyo/--1-dom--p1n>

## 这是什么？

是用 PureScript 写 SPA 的连载报道，是[hiruberuto](https://qiita.com/hiruberuto) 的[莫纳德的](https://qiita.com/hiruberuto/items/5321d8cebce7b87851f6)系列的[魔力](https://ja.wikipedia.org/wiki/%E3%81%B2%E3%81%A0%E3%81%BE%E3%82%8A%E3%82%B9%E3%82%B1%E3%83%83%E3%83%81_(%E3%82%A2%E3%83%8B%E3%83%A1)#%E5%90%84%E8%A9%B1%E3%83%AA%E3%82%B9%E3%83%88)。 本家是用 PureScript 写 mine craft 之类的浏览器游戏的家伙，非常有趣

另外，“PureScript 是什么，有什么优点”等元内容在本文中不进行解说。 (虽然您可能会在中途将 JS 框架、ECMAScript 和 PureScript 进行比较。)。 hiruberuto 先生的[纯函数型脚本语言 PureScript 的开头方法](https://qiita.com/hiruberuto/items/2316b58162cfec150460)等可以作为参考

## TL；博士；医生

*   [使用 slamdata/purescript-halogen](https://github.com/slamdata/purescript-halogen) 的话，可以简单且安全地构筑 SPA，非常厉害
    *   这次主要是这个 purescript-halogen 的解说。 如果有需求的话，可能会另外写 Halogen 的解说文章
*   如果用 purescript-halogen 写全堆栈的 SPA 的话，[`vladciobanu/purescript-halogen`](https://github.com/vladciobanu/purescript-halogen-example)非常有参考价值
    *   路由、global state 的管理、与外部 API 的交互、对话框的显示等，包含了[本家的`examples`](https://github.com/slamdata/purescript-halogen/tree/master/examples)中没有涵盖的内容
    *   虽然内容非常充实，但是抽象度相当高，而且还需要关于 Haskell 的`ReaderT`模式和摩德纳变换子的前提知识。 像我这样的初学者建议首先通读 Halogen 文档和示例源代码。 而且也请读这个连载！ ！ ( Halogen 的日语报道还几乎没有。 ）

## 这次要做的事

虽然想到要连载的地方很好，但是没有想到要制作的 SPA。 总之先输入以前在 Angular 上写的[宅男的网名就能瞬间收集到那个人的输出↓](https://github.com/entyo/WatchingU) 的移植进行下去。

[![](img/73472e050e000fe80344abdba3efe02e.png)T2】](https://camo.githubusercontent.com/39d233b953ada2c6d4e1e0551d6ffcfc857ce99e/68747470733a2f2f692e696d6775722e636f6d2f4935387963306d2e706e67)

第一次的这次，使用 purescript-halogen 只制作界面部分↓。

[![](img/bdf60fbe40ea497455fe22e310acf067.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oQOkvbeX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/ldjs3XS.gif)

在今后的连载中，每个用户( user1、user2、……)都可以进行输出的时间线，风格也将进行猜对，最终将脱胎换骨为更帅、没有运行时错误的 APP 应用。

源代码是[这里是](https://github.com/entyo/WatchingU/tree/purs)。

## 继续下去

### 用 parcel-bundler 准备开发环境

如果是 SPA 开发，最低限度希望有 bundler 和热重新加载有效的 dev-server。 可以通过使用`ethul/purs-loader`来使用 Webpack，如果今后不打算特别细致地定制的话，使用 Parcel 就轻松了吧。 justinwoo 的[easy pure script bundling with parcel](https://qiita.com/kimagure/items/24e6d3a0f47814c9630b)提供了参考。

另外，这是一个遗憾的消息，在 PureScript 中，很多函数被本地化，即使没做什么，捆绑后的 JS 文件也会变得非常大。

以前在 rollup.js 上强制停止卡里化(怎么做？ )我记得在网上的某个地方看到过有缩小尺寸的插件，但是调查了一下也没有找到……

### purescript-halogenでUIを構築する

接下来，SPA 开发希望的东西，果然还是 UI 库(内置的框架)吧。 关于 PureScript 的 UI 库，hiruberuto 先生用[PureScript 的 UI 库总结](https://qiita.com/hiruberuto/items/ec3f58c7742e79e3885a)进行了总结。

在这里列举的内容中，我最近在意的是已经在 Production 中使用的两个库。 一个是 purescript-halogen，由名为 slamdata 的启动程序开发，用于自己的服务。 如果从之前的总结报道中引用特征的话，

> *   内容的虚拟数字正射影像图实现，纯脚本制造的 pure script-卤素-vdom
> *   Throw the type of action update status like Flux and elm-html
> *   Including status update and sub-component query T7) a mechanism that can be statically simulated whether the combination of connected elements and attributes is correct.
> *   The development is centered on a company called slamdata. Because it seems that products using Halogen are actually provided, as an actual achievement, there is no doubt that the top one of them
> *   Samples and documents are the most complete, and there are many computers and users.
> *   And libraries for routers to write CSS securely are also being developed in parallel (other UI programs) separately from the main body, and there are people who make svg support (purescript-halogen-svg)

这样给人留下了相当好的印象。

正如刚才写的那样，这次应该是使用这个 purescript-halogen，但是最近名为 Lumi 的启动采用了 PureScript，还是公开了自己制作的库。 [purescript-react-basic](https://github.com/lumihq/purescript-react-basic) 。 PureScript 的采用和库的诞生经过在[官方的报道](https://medium.com/fuzzy-sharp/purescript-and-haskell-at-lumi-7e8e2b16fb13)中有记载，但主要以现有 React 组件的再利用为目的。

#### 纯脚本-卤素入門

在 purescript-halogen 中，在组件树中生成 UI，例如 React.js。 这次大致设想以下组件树。

[![](img/1416cd33d1eb4277ea66dc219fee6281.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V5oA7M1t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/OfLqwuV.png)

`UserAdd`组件包含`<input type="text">`要素，用于输入宅男的网名。 `UserList`组件是`UserTimeLine`组件的列表。 迁移前的画面中，用户输出所在的一列相当于`UserTimeLine`，列整体的集合相当于`UserList`。

组件中可以分别定义`Query`、`State`、`Input`、`Output`。 各自是什么样的东西，写得相当粗略是这样的。

*   `Query`:是代数数据类型，列举了变更组件状态(`State`)的命令。
    *   i.e.)`Counter`如果是组件的话，则为`Increment`、`Decrement`
*   `State`: React.js 的那个。 每当 State 被更新时，view (在 DSL 中定义。 SPA 的话应该是 HTML。 )来上载修改后的文件。
*   `Input`:关于存在父子关系的组件，从父代传递给子代的数据。
    *   如果父母的`State`被更新，则`Input`也会自动更新。
*   `Message`:关于存在亲子关系的组件，从子发送到父母的数据。 用 Angular 说的`Event`。

`UserAdd`以组件为例。

```
module UserAdd where

import Prelude

import Data.Maybe (Maybe(..))
import Data.Symbol (SProxy(..))
import Halogen as H
import Halogen.HTML as HH
import Halogen.HTML.Events as HE
import Halogen.HTML.Properties as HP

type State = { userID :: Maybe String }
initialState = { userID: Nothing } :: State

data Query a
  -- request
  = GetUserID (String -> a)
  -- action
  | UpdateUserID String a
  | AddUserID a

type Slot = H.Slot Query Message

_userAdd = SProxy :: SProxy "userAdd"

data Message = AddedUserID String

userAdd :: forall m. H.Component HH.HTML Query Unit Message m
userAdd =
  H.component
    { initialState: const initialState
    , render
    , eval
    , receiver: const Nothing
    , initializer: Nothing
    , finalizer: Nothing
    }
  where

  render :: State -> H.ComponentHTML Query () m
  render state =
      HH.div_
      [
        HH.input
          [
            HP.type_ HP.InputText,
            HP.placeholder "@e_ntyo",
            HE.onValueChange (HE.input UpdateUserID)
          ]
      ,
        HH.button
          [ HE.onClick (HE.input_ AddUserID) ]
          [ HH.text "追加" ]
      ]

  eval :: Query ~> H.HalogenM State Query () Message m
  eval = case _ of
    GetUserID reply -> do
      _userID <- H.gets _.userID
      case _userID of
        Nothing -> pure (reply "")
        Just id -> pure (reply id)
    AddUserID next -> do
      _userID <- H.gets _.userID
      case _userID of
        Nothing -> do
          H.raise $ AddedUserID ""
          pure next
        Just id -> do
          H.raise $ AddedUserID id
          pure next
    UpdateUserID id next -> do
      H.modify_ (_ { userID = Just id })
      pure next 
```

Enter fullscreen mode Exit fullscreen mode

在`State`中定义了`{ userID :: Maybe String }`这一类型(称为记录型)。 也就是说，从`<input>`要素设定的用户 ID 要么被设定，要么什么都没有。

`Query`中定义了一种类型为`request`的查询和一种类型为`action`的查询的数据类型。 两者都表示可以变更组件的`State`的命令，`request`正如其名，在评价该命令时可以返回某些信息。

评价`Query`的函数是`eval`，`Query`是代数数据类型，所以可以用模式匹配写得很好(当然如果有遗漏的地方，编译器会告诉你)。 `eval`的型号为`Query ~> H.HalogenM State Query () Message m`，这个可以通过将`Query`转换为`H.HalogenM State Query () Message m`的摩纳德来读取。 `~>`似乎表示关口的更换，据说圈论中有**自然变换**( Natural Transformation )，但圈论完全没有，所以就不做了

这里值得注意的是以下几点。

*   在`action`的查询`AddUserID`和`UpdateUserID`的评价中，分别向母组件发送`Message`或对`State`进行变更后，返回在`pure next`中提高了`a`型的值的内容
    *   在`UpdateUserID`中，除了`a`型的值之外，表示用户 ID 的`String`型的值也取为自变量。
    *   `AddUserID`发送的`Message`被`Container`组件句柄，此时`State`被更新，反应性地向`UserList`传递由`Input`定义的数据。
        *   这些处理和定义都写在各个组件的文件中。
*   在对`request`的查询`GetUserID`的评价中，作为`pure (next id)`，除了`next`之外还返回了`String`型的用户 ID。

每个请求的评估都在`do`语法中进行，除了纯粹的计算和这个模态中允许的处理以外，其他都将发生编译错误。 这个相当可靠或者说是安全的。

另外，感觉良好的人可能会注意到，`H.HalogenM State Query () Message m`的`m`意味着可以选择基底的莫纳德。 主要使用的是`Aff`(用于 PureScript 异步处理的摩多)，理由是在最终接收并处理根组件的`runUI`函数中`m`必须为`Aff` 详情在[这里](https://github.com/slamdata/purescript-halogen/blob/master/docs/3%20-%20Handling%20effects.md)。 选择了`Effect`而不是`Aff`的情况下，好像会用[`hoist`](https://pursuit.purescript.org/packages/purescript-halogen/docs/Halogen.Component#v:hoist)这个函数来匹配模型，但是因为是没有使用过的[文档也还没有的](https://github.com/slamdata/purescript-halogen/tree/master/docs#the-halogen-guide)，所以不太清楚。

`render`函数是接收`State`并返回 html 的函数。 html 用自己的 DSL 记述，正如 hiruberuto 先生的文章所示，如果没有好好设定合适的属性和事件，就会发生编译错误。 还有支持 Halogen 的[typed css 的库](https://github.com/slamdata/purescript-halogen-css)，今后打算使用这个。

另外，由于 Halogen 在内部使用的是 vdom，所以我想这并不是每次都会认真地描绘出来，而是会好好更新差异。 因此，在列表类的组件中会定义像 React.js 中所说的`key`那样的东西。 这次就不谈这一带的解说了。

通过定义这样的组件，感觉可以相当安全地构筑 UI。 当然，仅靠组件的`Input`、`Message`很难进行数据交换，因此 tl； 像在 dr 中介绍的[样品](https://github.com/vladciobanu/purescript-halogen-example)那样，今后需要用于处理 global state 的机构。

## times to inform.

这个连载最大的乐趣是考虑副标题。 什么是 DOM 拼贴画啊……你觉得是什么？ 我不知道。

下次以后还将运用 Halogen 以外的库，积极推进迁移工作。

*   用`slamdata/purescript-affjax`+ `justinwoo/purescript-simple-json`安装用户的输出时间线
    *   用 Angular 写的时候可以用 rxjs 实现轮询，但是用 PureScript 怎么做比较聪明呢？ 只能踏实地用`setInterval`写下去吗……
*   在`thomashoneyman/purescript-halogen-formless`中验证用户名的输入
    *   虽说是表格，但也只有一个`<input>`，因为看起来很有趣，所以想用一下
*   类型化 css
    *   这个已经在努力了，感觉很好啊…。

下次以后也一定要来读[哦](https://detail.chiebukuro.yahoo.co.jp/qa/question_detail/q13152924193)！ 谢谢您陪我到最后