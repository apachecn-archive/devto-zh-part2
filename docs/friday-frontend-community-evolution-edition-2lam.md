# 星期五前端:社区进化版

> 原文：<https://dev.to/kball/friday-frontend-community-evolution-edition-2lam>

本周的“另一个精彩”栏目中有一些有趣的事情。无论是在改进/增加 web 开发人员参与 CSS & JS 规范方面，还是在合并支持 JavaScript 开源项目的两个基础方面。

下周我会在 Node + JS Interactive 为 JSParty 采访人们；我会看看是否能确定 Node.js 和 js 基金会的一些成员，以了解合并意味着什么。

同时，希望你喜欢这些链接的其余部分。周末愉快！

最佳，

来自 ZenDev 的 KBall

附注:我听你们很多人说学习 Vue.js 是你们今年想要完成的事情…我正在做一个项目来帮助你们完成这个项目。请在接下来的几周内关注一项公告！

#### CSS&SCS

##### [以正确的方式创建水平滚动容器【CSS 网格】](https://medium.com/@dannievinther/creating-horizontal-scrolling-containers-the-right-way-css-grid-c256f64fc585)

这是一篇组合文章，既讨论了创建水平滚动容器时的设计问题，也讨论了使用 CSS grid 的实现。

##### [【CSS 的形状】](https://css-tricks.com/the-shapes-of-css/)

超级酷的文章，展示了如何使用一个元素和 CSS 创建各种形状。我用过很多三角形和圆形的，但是后来的一些非常酷。我想我最喜欢的是阴阳。介意。吹了。

##### [Grid Vs Flexbox:你该选哪个？](https://www.webdesignerdepot.com/2018/09/grid-vs-flexbox-which-should-you-choose/)

当读到标题时，我的直觉反应是“两者都是，它们是免费的！”但幸运的是，事实证明作者是在同一页上，他心中的选择更多的是一个“具体情况”的选择。这篇文章实际上做得很好，展示了 flexbox 和 grid 中的许多工作方式，并给出了不同用例的折衷方案。

##### [现在是 2018 年:你不应该写香草 CSS](https://dev.to/bnevilleoneill/its-2018-you-shouldnt-be-writing-vanilla-css-50ff-temp-slug-8409804)

我原以为这是在吹捧 CSS 框架，但实际上这是对 CSS 预处理程序的概述(一路 SCSS！)、后处理器(postCSS 功能惊人的强大)以及 CSS-in-JS 选项。看看这个。

##### [了解遗产代码库](https://csswizardry.com/2018/07/getting-to-know-a-legacy-codebase/)

这是从挖掘遗留 CSS 代码库的角度来看的，但我认为这里的经验是普遍适用的。如果没有一个了解所有代码的大师，你如何学习一个代码库呢？这是一个非常真实而有趣的问题，本文总结了一些非常有用的技术来解决这个问题。

#### JavaScript

##### [vue . js 下一次迭代的计划](https://medium.com/the-vue-point/plans-for-the-next-iteration-of-vue-js-777ffea6fabf)

这是大事！Vue.js 的创建者和项目负责人尤雨溪公布了该框架下一次重大迭代的一系列计划。有一些令人兴奋的技术细节:一个更小、更模块化的代码库，巨大的性能改进，片段和门户支持，并使用 TypeScript 构建。但是我更兴奋的是社区进程公告——一个正式的 RFC 进程和一个针对旧浏览器的明确的“兼容性”构建。Vue 长大了！

##### [JavaScript Arrow 函数:如何、为什么、何时(以及何时不)使用它们](https://dev.to/kball/javascript-arrow-functions-how-why-when-and-when-not-to-use-them-253e)

(Bias alert -我写了这篇文章)现代 JavaScript 中最受欢迎的特性之一是引入了箭头函数，有时称为“胖箭头”函数，利用了新的标记`=>`。然而，像工程中的任何事情一样，箭头函数也有积极和消极的一面。本文首先回顾了箭头函数是如何工作的，然后深入研究了箭头函数在哪些方面改进了我们的代码，最后深入研究了一些箭头函数并不是一个好主意的例子。

##### [JavaScript 要点:类型&数据结构](https://codeburst.io/javascript-essentials-types-data-structures-3ac039f9877b)

JavaScript 中的“缺乏类型”是对这种语言最常见的批评之一，也是使用类似 TypeScript 的东西的原因。然而，这并不是说 JavaScript 完全没有类型——它只是有非常弱的动态类型，在幕后有许多强制。如果你不明白所有这些是如何工作的，这篇文章是必须的。

##### [React 和 Angular 应用中服务器端渲染对比](https://dev.to/treyhuffine/a-comparison-of-server-side-rendering-in-react-and-angular-applications-mgi-temp-slug-2878332)

比较和教程的结合——首先让 SSR 在 Angular 应用程序中工作，然后在 React 应用程序中工作。如果你正在考虑在现有的应用程序中这样做，这肯定会有所帮助。如果没有，我肯定会建议从一个更高级别的框架或模板(比如 React 的 Next.js)开始，它会为你做很多这样的配置。旁注:这无疑加强了我对角度反应的倾向…并使我好奇的想看到与 Vue 的类似比较。:)

##### [在单个文件中写入多个 Vue 组件](https://dev.to/hugo__df/writing-multiple-vue-components-in-a-single-file-hgj)

在单个文件中编写小的“助手”组件的能力是 React 的模式之一，我真的很想念它。单个文件组件很好，但是有时你有一些东西只在一个组件内部使用，把它分割成另一个文件会产生很多样板文件。这篇文章介绍了在 Vue 中实现“一个文件中多个组件”的多种方法，尽管老实说没有一种方法像 React 版本那样自然。

#### 其他牛气

##### [W3C 中的 Web 开发者代表](https://alistapart.com/article/web-developer-representation-in-w3c)

这真的很有意思。一个荷兰 web 开发者组织正在考虑成为 W3C 成员，并付费让一些 web 开发者社区的代表参与规范过程(第一个是 Rachel Andrew——你可以在这里[阅读她的帖子](https://www.smashingmagazine.com/2018/09/representing-web-developers-w3c/))。与此形成对比的是，绝大多数会员都是浏览器厂商的代表，而不是最不感兴趣的群体。他们还呼吁其他网站开发者团体更多地参与进来。我不确定谁可能是这里的好例子——这有点像一个专注于 webdev 的专业团体。有人知道谁能给我指一个吗？

##### [Node.js 基金会和 js 基金会宣布有意创建联合组织](https://www.linuxfoundation.org/news/2018/10/node-js-foundation-and-js-foundation-announce-intent-to-create-joint-organization-to-support-the-broad-node-js-and-javascript-communities/)

我不完全确定这将意味着什么，但我认为总的来说应该是积极的。JS Foundation 以 jQuery foundation 开始，最终扩展到支持许多开源项目，包括 lodash、ESLint 和 webpack。Node.js 基金会是在 2014 年一个大型社区分裂后，出于对 Node.js 非公司治理的渴望而成立的。两者都在 Linux 基金会的保护伞下，我认为这次合并应该会澄清“节点”和 JavaScript 世界其他部分之间的一个似是而非的区别。

##### [最佳 HTML、CSS、Javascript 练习:Chrome 扩展](https://medium.com/@thejungwon/best-html-css-javascript-practice-chrome-extension-ae4e5e7839e)

创建 Chrome 扩展的步骤的一个很好的演示。目标是相对的新手——刚刚完成 Codecademy 的人——但是对任何从未做过扩展并且想做的人都有用。

##### [没人教你的最重要的技能](https://medium.com/personal-growth/the-most-important-skill-nobody-taught-you-9b162377ab77)

从几个月前，但它对我说了足够多的话，我想把它包括在内(我想我不是唯一一个发现它对我说话的人——我以前从未见过有这么多掌声的媒体文章！).谈论无聊的价值，面对自己的价值，了解自己的价值。如今(尤其是在我们这个行业),试图一直保持联系很容易，但花些时间独自了解自己也很重要。

##### [你真的了解 CORS 吗？](http://performantcode.com/web/do-you-really-know-cors)

在过去，服务器渲染的网页都存在于一个域中，CORS 是一个你不必太担心的东西。但是在当今这个 JAMStack、独立前端等等的世界里，这是每个 web 开发人员都可能在某个时候遇到的一个话题。如果你还不熟悉，你真的应该读读这篇文章。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】