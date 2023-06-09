# 每周用户界面挑战第 3 周第 2 天:添加结果条目、赞助商/畅销书指标

> 原文：<https://dev.to/geoff/weekly-ui-challenge-week-3-day-2-add-result-entry-sponsoredbest-seller-indicators-1pg5>

欢迎来到第 3 周，我每周 UI 挑战的第 2 天！第 3 周将关注一个**结果页面** UI 组件；在接下来的一周里的每一天，我都会挑选一两个(通常是相关的)子元素来实现。第二天，我们的目标是…

## 添加结果条目，赞助商/畅销书指标

再次回归基础！任何结果页面首先需要的是一个/多个条目来填充页面，因为如果没有它们，这将只是一堆空白。像我们所做的其他 UI 项目一样，这些结果应该易于浏览，具有良好的颜色对比，并显示相关的内容，以便对搜索者最有用。

此外，原始搜索查询显示在页面顶部，因此用户能够看到他们搜索的内容；这很容易被二级搜索栏取代，类似于谷歌的主搜索功能。

也像谷歌的搜索一样，如果有付费内容和/或广受好评的结果显示，我们的结果应该有某种指示。在我的例子中，我的设计是基于一个潜在的体育博客，所以我有一个特殊的背景颜色，表明链接到某人赞助的内容会首先出现。

根据我创作的原始设计，这是我为第二天准备的:

[![A results page component, with one result entry highlighted to indicate it is sponsored](img/9fe4129d7df74f57a1c322abf9568be1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cOdTLPp7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ha402qn8gf6tyl5z8soq.png)

我的实现相当简单，使用列表视图(目前)；如果你愿意，为了获得额外的积分，你可以使用你从第一周开始制作的电子商务清单，如果这符合你的设计的话。

我选择了一种褪色的绿色作为赞助内容的标志，旁边是一个互补的深色标签。最后，一个简单的自定义焦点状态给原本简单的页面增添了一些特色。

## 现在轮到你了

我使用了 [React.js](https://reactjs.org) 和 [Storybook](http://storybook.js.org) 来开发我的实现，但是你可以使用任何你喜欢的技术栈！(提示:如果你用 [Vue.js](https://vuejs.org/) 或者 [Angular.js](https://angularjs.org) ，那些库还是可以用 [Storybook 的)](https://storybook.js.org/basics/slow-start-guide/)

如果你不愿意，你甚至可以不使用视图库；HTML 和 CSS-only(和非视图 JavaScript 库)组件是可能的，特别是对于这一步。

此外，请在评论中添加您的设计灵感的回复和/或图像！我很想看看你们都创作了什么样的设计。

编码快乐！🎉

### 第 3 周日历

1.  (周日 4/22)设计组件✅
2.  结果条目、赞助商/畅销书指标🎯
3.  网格/列表视图切换
4.  整理
5.  分页/加载更多
6.  100% a11y 分数
7.  调整、重构、修复