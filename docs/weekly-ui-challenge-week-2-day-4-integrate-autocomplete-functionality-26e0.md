# 每周 UI 挑战第 2 周第 4 天:集成自动完成功能

> 原文：<https://dev.to/geoff/weekly-ui-challenge-week-2-day-4-integrate-autocomplete-functionality-26e0>

欢迎来到我每周 UI 挑战的第 2 周第 4 天！第 2 周将关注一个**搜索栏** UI 组件；在接下来的一周里的每一天，我将选择一个设计的子元素来实现。第四天，我们的目标是…

## 集成自动完成功能

现在大多数搜索栏都有某种自动完成功能，让用户从你的网站/应用中找到最准确的网页/结果。对于像脸书和谷歌这样的搜索组件，需要一点机器学习，但我们今晚不会深入讨论。

这个组件只需要列出几个选项，这些选项可能是应用程序/网站希望出现的搜索最多或最突出的文章/结果。让用户不必选择自动完成选项就能进行搜索是最佳实践，但有时这对于某些类型的搜索实现是有意义的。

根据我创作的原始设计，这是我为第四天准备的:

[![A search bar component, with input text element, autocomplete dropdown, and submit button](../Images/38043e3f5127425bedd066ac25811151.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xIuzcvmg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zwy2kqa1jm8oe2z0xrnl.jpg)

我的实现提供了一些音乐流派，就好像它被添加到了一个音乐博客或研究网站；我使用 PayPal 的[降档](https://github.com/paypal/downshift)库，由[肯特·多兹](https://twitter.com/kentcdodds)创建和编写，用于自动完成功能。我有一点麻烦让它完全工作，所以我的编码版本不是 100%漂亮，但它会完成工作！

下面是自动完成功能的运行动画:

[![An animation showing the search bar component in use, with text input to the input element, a button to trigger a search form submission, an autocomplete dropdown, and a logger display showing the output below](../Images/4390655b606a08c64f7013707ab8f3e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QcARXZfR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/geoffdavis92/weekly-ui-assets/master/search-bar/day4/w2d4-animation.gif)

## 现在轮到你了

我使用了 [React.js](https://reactjs.org) 和 [Storybook](http://storybook.js.org) 来开发我的实现，但是你可以使用任何你喜欢的技术栈！(提示:如果你用 [Vue.js](https://vuejs.org/) 或者 [Angular.js](https://angularjs.org) ，那些库还是可以用 [Storybook 的)](https://storybook.js.org/basics/slow-start-guide/)

如果你不愿意，你甚至可以不使用视图库；HTML 和 CSS-only(和非视图 JavaScript 库)组件是可能的，特别是对于这一步。

此外，请在评论中添加您的设计灵感的回复和/或图像！我很想看看你们都创作了什么样的设计。

编码快乐！🎉

### 第二周日历

1.  (周日 4/15)设计组件✅
2.  Input field ✅
3.  Submit button ✅
4.  集成自动完成功能🎯
5.  过去的搜索词指示器
6.  100% a11y 分数
7.  调整、重构、修复