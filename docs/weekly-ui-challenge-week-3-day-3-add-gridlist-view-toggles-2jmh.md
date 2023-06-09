# 每周 UI 挑战第 3 周第 3 天:添加网格/列表视图切换

> 原文：<https://dev.to/geoff/weekly-ui-challenge-week-3-day-3-add-gridlist-view-toggles-2jmh>

欢迎来到第 3 周，我每周 UI 挑战的第 3 天！第 3 周将关注一个**结果页面** UI 组件；在接下来的一周里的每一天，我都会挑选一两个(通常是相关的)子元素来实现。第三天，我们的目标是…

## 添加网格/列表视图切换

控制 UI 显示的能力并不是一个经常包含的特性，但是它可以增强应用程序/网站的体验和/或效用。Instagram(用户个人资料视图)和 Google Drive 等用户界面中都包含这样的功能；通过移除多余的——但有时可能是有用的——信息，它们肯定有助于设计的可扫描性。

虽然设计是实现一个人的应用程序或网站愿景的一个非常重要的决定，但移交一些显示控制权可以向你的用户显示你关心他们的需求。

根据我创作的原始设计，这是我为第三天准备的:

[![A results page component in list view, with one result entry highlighted to indicate it is sponsored](img/ae8c71a88ffb43c00fded54b4f48c728.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b4pNW4ba--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cmm27su78o4j8defusbt.png)

[![A results page component in grid view, with one result entry highlighted to indicate it is sponsored](img/4d1759648ae5afc91dadc739f80ce3fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2hAse2WX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/egr6t09jvbs2qo07ej2w.png)

就像我们在这个挑战中创建的一些功能一样，当使用 cookies 或其他保存用户状态的机制保存首选项时，这个视图切换可能会工作得最好。但是，到目前为止我已经跳过了这一步。这个特性是一个简单的切换，它通过[样式的组件](https://styled-components.com)利用 React 的内部组件状态和动态样式。

我建议尝试新的(ish) [`grid` CSS API](https://developer.mozilla.org/en-US/docs/Web/CSS/grid) 来设计网格视图的样式；它没有你可能想象的那么可怕，而且它的威力惊人。

下面是该功能的动画效果:

[![A results page component switching between list and grid views](img/6c8ac9cfb72390aca73051d0a50f2111.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sYXdnJdY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f1kxwc5o0mjktd0fsb73.gif)

您可以在我的 Github pages 站点上查看我为这个项目编写的代码实现[。](https://geoffdavis92.github.io/weekly-ui/)

## 现在轮到你了

我使用了 [React.js](https://reactjs.org) 和 [Storybook](http://storybook.js.org) 来开发我的实现，但是你可以使用任何你喜欢的技术栈！(提示:如果你用 [Vue.js](https://vuejs.org/) 或者 [Angular.js](https://angularjs.org) ，那些库还是可以用 [Storybook 的)](https://storybook.js.org/basics/slow-start-guide/)

如果你不愿意，你甚至可以不使用视图库；HTML 和 CSS-only(和非视图 JavaScript 库)组件是可能的，特别是对于这一步。

此外，请在评论中添加您的设计灵感的回复和/或图像！我很想看看你们都创作了什么样的设计。

编码快乐！🎉

### 第 3 周日历

1.  (周日 4/22)设计组件✅
2.  结果条目，赞助/畅销书指标✅
3.  网格/列表视图切换🎯
4.  整理
5.  分页/加载更多
6.  100% a11y 分数
7.  调整、重构、修复