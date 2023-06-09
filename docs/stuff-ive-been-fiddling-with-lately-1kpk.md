# 我最近一直在摆弄的东西

> 原文：<https://dev.to/seanwalsh/stuff-ive-been-fiddling-with-lately-1kpk>

*最初发表于[https://swalsh.org/blog/stuff-ive-been-fiddling-with-lately](https://swalsh.org/blog/stuff-ive-been-fiddling-with-lately)T3】*

在过去的一两个月里，我一直在尝试各种语言和框架，但是还没有足够的经验来撰写一篇有深度的博文。然而，我确实有一些意见，所以我想我应该在一篇文章中收集一些小片段。希望将来能写更多关于这些技术的文章。

## 反应原生

[的大粉丝反应原生](https://facebook.github.io/react-native/)。我做过一点 Objective-C/Swift/Java，但是从来没有喜欢过用它们来构建 ui。React Native 无疑让我更接近我所熟悉的东西。话虽如此，也不全是好事。经过多次反复试验，我最终选择了 [React 导航](https://reactnavigation.org/)作为我的路线解决方案。它并不完美，但很有效。主要竞争者[的 React 原生导航](https://github.com/wix/react-native-navigation)看起来很酷，但它的设置非常糟糕，没有真正令人信服的理由来切换。

我对 RN 路由最大的不满可能是我职业生涯的大部分时间都花在 web 应用程序上的副作用，但这感觉太不灵活了。例如，我正在开发的 hobby 应用程序使用抽屉导航，但也想要一个带有一些堆栈导航的持久标题，所以基本上是 React Navigation 提供的三个路由选项中的两个的组合。我无法在不将我的路由状态转移到 Redux 中的情况下实现带有上下文后退按钮的持久化标题，这是 React Navigation [反对](https://reactnavigation.org/docs/en/redux-integration.html)的建议。总的来说，不是很好的体验，但我做到了。

我期待着在未来更多地探索 RN。

## NativeScript

既然我们在 DroneDeploy 使用 Angular，我也想给 [NativeScript](https://www.nativescript.org/) 一个公平的机会。我对 Angular 和它的特质并不陌生，但我只是没有从 NS 那里得到和 RN 一样的快速开发氛围。虽然我在 Angular 上有更多的经验，但感觉我比在 RN 上犯了更多的错误。我最不喜欢的是种类繁多的[布局容器](https://docs.nativescript.org/ui/layouts#predefined-layouts) : FlexboxLayout、AbsoluteLayout、DockLayout、GridLayout、StackLayout 和 WrapLayout。我知道 NS 团队试图给你提供可靠的选择，但我觉得我花了大部分时间在每个组件上，试图决定我想要的布局。RN 的无处不在让我感觉更有效率。

我肯定会在未来更多地挖掘 NS，但如果我是一个新手，我会在这一点上强烈倾向于 RN。

## 领域数据库

我过去用于本地应用的唯一数据库解决方案是 sqlite，所以我想扩展一下，尝试一些新的东西。我登陆了[领域](https://realm.io/products/realm-database/)，这是一个为本地应用设计的 NoSQL 数据库。我不太喜欢 NoSQL 的大规模数据，但我承认它非常适合快速的本地数据库。我试图通过 [redux-observable](https://github.com/redux-observable/redux-observable) 让 Realm 与我的 redux 商店同步，但最终导致的问题比我解决的更多，所以我最终将其从 Redux 中删除。如果我在做一个严肃的项目，我可能会花更多的时间来完成它，因为它有点受限。

最大的问题:没有级联删除。领域模型支持关系(1:m 和 m:m ),但是留下孤立记录非常容易，除非您非常注意删除逻辑。我可以看到这对于一个复杂的数据模型来说变得非常有问题。

## 榆树

在避开[榆树](http://elm-lang.org/)很长一段时间后(也没有什么好的理由)，我终于屈服了，并浏览了他们优秀的[教程](https://www.elm-tutorial.org/en/)。该教程是非凡的，它让我对 Elm 程序的语言和典型架构有了坚实的理解。我认为这是一种简洁的语言，我希望在未来用它来构建一些东西，但我现在没有任何好的想法，所以它可能会在我的“某一天”书架上搁置一段时间。

两个小问题:

*   复杂的业务逻辑可能会变得非常冗长。这可能部分是因为我对这门语言不熟悉，但总的来说，感觉我打了很多字。
*   我真的不关心视图/模板语法。我想我已经习惯了视图的 XML 式语法，这让我有点不知所措。

## 原因

我真的不能进入[原因](https://reasonml.github.io/)。我认为这是一个很酷的项目，但我只是没有看到它给了我比常规的 TypeScript + React 更强的优势。还有，我完全是[这些人中的一员](https://twitter.com/marcelcutts/status/1002153230757978113?s=19):

> 奇怪的普通交换
> 
> “所以你可以通过@reasonml 和 React 获得所有这些神奇的东西，这些东西可以节省你的时间，让你晚上睡得很香。”
> 
> “是的，但是你必须写`ReasonReact.string`来把文本放到组件中，我不在家”

## 生锈

我完全意识到我在喝苦艾酒，但是我真的想喜欢上[铁锈](https://www.rust-lang.org/en-US/)。我真的很喜欢这个语法，但是语言概念还没有和我产生共鸣。这是 web 开发的一个相当大的转变，所以我认为我只需要坚持下去，但这很难理解。有一个受榆树启发的 UI 机箱叫做 [Relm](https://github.com/antoyo/relm) ，看起来很有前途，我想如果我再给 Rust 一次机会，我可能会努力专注于这个。

## 其他玩意儿？

想让我相信这些都是错的吗？或者有我应该玩的东西？给我留言评论！