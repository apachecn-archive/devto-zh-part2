# 每周用户界面挑战第 2 周第 6 天:在 a11y 审计中获得 100 分

> 原文：<https://dev.to/geoff/weekly-ui-challenge-week-2-day-6-score-100-in-an-a11y-audit-2157>

欢迎来到我每周 UI 挑战的第 2 周第 6 天！第 2 周将关注一个**搜索栏** UI 组件；在接下来的一周里的每一天，我将选择一个设计的子元素来实现。第六天，我们的目标是…

## 在 a11y 审计中得分 100%

可访问性——或者 a11y，读作“ally”——是现代 web 开发中一个非常重要的概念。无论您是在构建 web 应用程序还是简单的静态登录页面，让网页具有可访问性应该是每个开发人员和设计人员首先考虑的问题。对许多人来说，这是事后的想法，但 a11y 正迅速成为 SEO 排名的一个指标，对你的网页/应用程序的法律完整性至关重要。让用户更容易实际使用你的网站/应用/小部件也是一个好主意和策略。

A11y 技术跨越了相当多的领域和学科，从 HTML 属性和 CSS 状态，到设计中使用的颜色(您可能会注意到，我在设计中使用的大多数颜色在其相关领域的 WCAG 对比度至少为 AA)和除非使用屏幕阅读器否则没有人会与之交互的文本。为了测试一个网页的可访问性，有几个工具在网络上浮动。一个这样的工具是 **[a11y.css](https://ffoodd.github.io/a11y.css/)** 。

a11y.css 是一个很棒的工具，它使用 css 定位来查找代码中的 a11y 错误、警告和建议提示，并以内联方式向您显示消息。您甚至可以配置该工具只针对某些级别的警告。当我开始致力于提高可访问性时，这个工具是我使用的第一个工具。

我使用的另一个工具是[Google light house accessibility audit](https://developers.google.com/web/tools/lighthouse/)这个工具将检查 a11y.css 做的所有类型的事情，等等。它允许对调试进行更多的控制，因为它会列出有问题的元素，而对该列表的斥责会突出显示 DOM 中的那些元素，就像普通的 devtools 所做的那样。我还发现 Lighthouse 比 T2 的许多 T3 更容易解决问题，因为它还会链接到每个错误的解释页面。

我在这篇文章的脚注中链接了几个关于 a11y 技术和测试的工具和资源，我希望你能使用它们并探索帮助使网络对每个人都有用的方法！

## 现在轮到你了

我使用了 [React.js](https://reactjs.org) 和 [Storybook](http://storybook.js.org) 来开发我的实现，但是你可以使用任何你喜欢的技术栈！(提示:如果你用 [Vue.js](https://vuejs.org/) 或者 [Angular.js](https://angularjs.org) ，那些库还是可以用 [Storybook 的)](https://storybook.js.org/basics/slow-start-guide/)

如果你不愿意，你甚至可以不使用视图库；HTML 和 CSS-only(和非视图 JavaScript 库)组件是可能的，特别是对于这一步。

此外，请在评论中添加您的设计灵感的回复和/或图像！我很想看看你们都创作了什么样的设计。

编码快乐！🎉

### 第二周日历

1.  (周日 4/15)设计组件✅
2.  Input field ✅
3.  Submit button ✅
4.  集成自动完成功能✅
5.  过去的搜索词指标✅
6.  100% a11y 分数🎯
7.  调整、重构、修复

### 资源

*   [a11y.css](https://ffoodd.github.io/a11y.css/)
*   [a11y 项目](https://a11yproject.com)
*   [`aria`MDN 上的技术](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques)
*   [谷歌灯塔](https://developers.google.com/web/tools/lighthouse/)
*   [WCAG 色彩对比检查器](https://webaim.org/resources/contrastchecker/)