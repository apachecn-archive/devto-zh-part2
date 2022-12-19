# 周五前端:看看 Vue 版

> 原文：<https://dev.to/kball/friday-frontend-check-out-the-vue-edition-26f0>

希望你度过了愉快的一周！我确实这样做了——从一个充满激情的会议中回来，对我来说总是会有一个好的一周。走出自己正常的空间，重新激励自己，真好！

本周的时事通讯特别关注 Vue.js。整个 JavaScript 部分都关注 Vue…这是为了纪念我最近宣布的新项目[LearnVueJS.com](https://www.learnvuejs.com/)提供关于 Vue.js 基础的免费网络研讨会。如果你错过了那个公告，你可以在这里查看。

如果你不喜欢 Vue，也不要担心——下周我们将回到我们常规的跨 javascript 世界的日程，现在也许可以看看一些 CSS 和其他很棒的帖子。本周在 CSS 版块有几个特别令人兴奋的。

希望你有一个愉快的周末！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [CSS 能做到吗？](https://9elements.com/io/css-border-radius/)

我把标题全部大写，因为这让我大吃一惊。我经常使用边框半径，但通常都设置相同的半径。我有一个模糊的想法，你可以独立设置不同角落的半径，但我不知道你可以独立设置垂直和水平半径…这里有这么多令人惊讶的可能性。

##### [CSS 以前的兄弟选择器以及如何伪造它们](https://medium.freecodecamp.org/how-to-make-the-impossible-possible-in-css-with-a-little-creativity-bd96bb42b29d)

这是一个很好的练习，探索了兄弟(+)和后续兄弟(~)选择器，以及如何使用它们来伪装“上一个兄弟”选择器。即使你不想这样做，这也会给你一些很好的实践，让你思考这些兄弟选择器的应用。

##### [有效 CSS 内容](https://css-tricks.com/valid-css-content/)

我一直在使用 content 属性，因为我用 before/after 伪元素做了很多事情，它们需要设置内容才能显示。但是我从来没有想过要探索用内容呈现的可能性的界限…这真的很疯狂。

##### [负网格线](https://css-irl.info/negative-grid-lines/)

我使用 CSS Grid 的次数还不够多，还没有把它融入到我的心理模型中，所以这花了我大约 3 次通读和一些戳在演示中，但后来它让我大吃一惊。

#### JavaScript

##### [如何将 Sass 文件导入到 App 中的每个 Vue 组件](https://css-tricks.com/how-to-import-a-sass-file-into-every-vue-component-in-an-app/)

这太棒了。使用 Vue 的单个文件组件方法可以让你隔离每个组件的风格，但是正如文章所说——许多设计属性是完全全球化的，因为内聚性在设计中是很重要的。没问题，如果这些属性被封装在文件中，那么将它们自动导入到 Vue 组件中是非常简单的。太棒了。

##### [高级 Vue 调试:侦探故事](https://vuejsdevelopers.com/2018/10/16/vue-js-advanced-debugging/)

(偏见警告——我写的)像 Vue.js 这样的现代框架为你做了很多。他们管理 DOM 更新，维护组件生命周期，等等。像 Nuxt.js 这样的高层框架甚至更多的层会照顾到你。但是一个关键问题是——当事物崩溃时会发生什么？当出错时，在代码之外的幕后发生了这么多事情，你如何调试它？这几乎就像解开一个谋杀之谜。以至于我决定谈论它的正确方式可能是把它作为一个正在解开的谜来讲述...

##### [关于 Vue CLI 3 你需要知道的事情](https://dev.to/progresstelerik/what-you-need-to-know-about-vue-cli-3-56h5-temp-slug-3068326)

在 Vue CLI 的最新版本中有一些不错的东西。我认为我最喜欢的是 frontend CLI 工具的最新版本，它支持构建配置，而不会强迫您管理整个构建。在旧世界中，要进行定制，您必须“弹出”一个构建，并将其全部提交到您的源代码中，从而永远拥有所有权。在新的世界中，您可以“合并”您的构建定制，同时允许工具仍然管理大部分配置，从而让您在工具更新时轻松地更新到构建的新版本。

##### [用 Vue.js 指令和交集观察器懒加载图片](https://css-tricks.com/lazy-loading-images-with-vue-js-directives-and-intersection-observer/)

这是一个新的浏览器 API(交叉点观察器)和 Vue 的指令系统的完美结合，可以创建一个超级干净的方法，在你需要的时候轻松加载图像。我喜欢它。

##### [nuxt . js 入门](https://dev.to/progresstelerik/getting-started-with-nuxtjs-1n98-temp-slug-3155684)

Nuxt 是使用 Vue 的一种非凡方式。它可以做出许多常见的决定&设置问题，只是带有一组漂亮的缺省值，但保持足够的可定制性，如果你想的话，你可以避开这些缺省值。去年我几乎所有的 Vue 工作都在使用 Nuxt。如果你有兴趣开始，这是一个很好的入门。

#### 其他牛气

##### [可访问性备忘单](https://moritzgiessmann.de/accessibility-cheatsheet/)

我认为这可以变得更彻底——它主要涵盖了设计和测试，并且在相当高的水平上——但是这是一个很好的起点。我特别喜欢一些建议的测试。

##### 

说到可访问性…这似乎是一件大事。易访问性社区的人们已经对古腾堡拉响警报有一段时间了，但是 Wordpress 易访问性团队领导[刚刚辞职](https://rianrietveld.com/2018/10/09/i-have-resigned-the-wordpress-accessibility-team/)，这看起来将会非常混乱。

##### [介绍 GitHub 动作](https://css-tricks.com/introducing-github-actions/)

虽然技术上与前端无关，但这似乎足够大，值得强调。Github 刚刚宣布能够设置本质上定制的 IFTTT 风格的代码触发器，作为平台上发生的事情的结果。这为一些更加强大的持续集成解决方案以及一些其他非常有趣的可能性打开了大门。

##### [为认知差异而设计](https://alistapart.com/article/designing-for-cognitive-differences)

有趣的是，看看你的设计和实现选择如何影响你的网站/应用程序如何被不同形式的智力挑战的人使用。我发现关于焦虑的部分特别有帮助(可能是因为我知道很多人都在焦虑中挣扎)，但它们都很好。

##### [开始绩效预算](https://addyosmani.com/blog/performance-budgets/)

Addy Osmani 一直在谈论 web 和 JavaScript 性能；我喜欢这篇文章的是它的战术性。它不是向你解释为什么你应该关心，而是给你明确的过程和工具，你可以用来提高你的应用程序的性能。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】