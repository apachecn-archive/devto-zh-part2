# 用 Web 组件分离您的前端关注点

> 原文：<https://dev.to/hfjallemark/separating-your-front-end-concerns-with-web-components--4p85>

这篇文章原本是我发布的一个小型 Github 项目的自述文件，我正在探索如何结合使用 [Elm](http://elm-lang.org/) 和 [web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)。写完之后，我意识到它真的更通用，适用于任何框架——因此把它从自述文件中抽出来放到一个单独的帖子中。

我喜欢利用本地 web 组件来构建可重用 UI 组件的想法，不管您使用哪种技术。我相信它有几个关键的优势，我想分享一下。

## 框架不可知

您可以将它们与您目前使用的任何框架一起使用。如果你在一家较大的公司工作，不同的团队可能使用不同的技术，web 组件通常可以使用其中的任何一种(甚至是虚拟 DOM ),因为它们与常规的 HTML 元素没有什么不同。让 web 组件成为最小公分母可以确保你的 UI 组件可以和任何技术一起工作——甚至是和 Elm 一起工作的[！](https://github.com/hfjallemark/elm-web-components)

## 分离顾虑

构建复杂的软件具有挑战性，减少认知负荷是成功的关键工具之一。分离关注点是减少认知负荷的最有效的方法之一——例如，一起编写数据库查询和 CSS 需要同时处理两个完全独立的概念。我记得 20 年前在经典的 ASP。幸运的是，我们不再这样做了！当分离关注点时，我们引入约束；人为的或技术上的界限和约束往往会[解放你的思想](https://www.fastcompany.com/3067925/how-constraints-force-your-brain-to-be-more-creative)。

出于同样的原因，你应该避免将数据库查询和 CSS 一起编写，我想你也应该避免将 UI 和业务逻辑一起编写。我发现将两者分开是一个有用的模式。在我最近开发的几个 React 应用中，UI 组件已经完全从我的“应用”组件中分离出来。在某些情况下是一个单独的 repo / npm 包，在某些情况下只是我的应用程序中的一个单独的文件夹。UI 组件包含所有的样式、布局、本地 UI 状态(例如，日历弹出窗口是否打开),它们声明性地公开了用于传递数据和事件处理程序的属性/属性——听起来熟悉吗？是的，这正是原生 HTML 元素的工作方式。我相信将 UI 与业务逻辑和状态分开对于减少认知负荷有着深远的影响。例如，同时看到`calendarPopupOpen`和`customerHasPendingOrders`实在是太多了，我无法同时记住。

到目前为止，我的 UI 组件一直是常规的 React 组件，但是随着 web 平台的成熟，我认为考虑让它们成为常规的 web 组件是有意义的。随着有趣的技术如 [lit-html](https://github.com/Polymer/lit-html) 和 [lit-element](https://github.com/Polymer/lit-element) 的出现，我认为构建定制组件，甚至更复杂的组件如 [React Select](https://jedwatson.github.io/react-select/) ，将成为一种令人愉快的体验，与我们习惯并喜欢的关于 React 的声明模式相同。再加上 CSS 变量最终在所有主流浏览器中被采用(还有 [:part and :theme 伪元素](https://meowni.ca/posts/part-theme-explainer/)正在讨论中)，我认为即使是使用本地技术的定制组件主题化也会变得轻而易举。

构建 web 应用程序的确是一个激动人心的时刻！