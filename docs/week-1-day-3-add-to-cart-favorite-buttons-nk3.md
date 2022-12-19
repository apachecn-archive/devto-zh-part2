# 第 1 周第 3 天:添加到购物车，收藏按钮

> 原文：<https://dev.to/geoff/week-1-day-3-add-to-cart-favorite-buttons-nk3>

欢迎来到我每周 UI 挑战的第一周，第三天！正如我在[公告帖子](https://dev.to/geoff/announcing-weekly-ui-challenge-h87)中所说的，第一周将关注**电子商务列表** UI 组件；在接下来的一周里的每一天，我都会挑选一两个(通常是相关的)子元素来实现。第三天，我们的目标是…

## 添加到购物车，收藏按钮

我的列表包含所有要素:标题、价格、照片，对吗？都吃完了？不…

我忘了让它可供购买，这是一个非常重要的步骤，除非你正在运行一个橱窗购物电子商务平台！

根据我创作的原始设计，这是我为第三天准备的:

[![ecommerce listing showing orange knitted winter hat, the name of item "Thinsulate Winter Cap", and the $34.99 price, and a "add to cart" button in blue, on the bottom](../Images/c513630cddabb26ae5d6f5d930a4c539.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---wrQLBVy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/geoffdavis92/weekly-ui-assets/master/ecommerce-listing/day3/w1d3-final.png)

就像产品的名称和价格一样，购买产品或将其添加到您的购物篮/购物车的方式应该在电子商务列表中突出显示。我选择使用块级按钮，或者 100%宽度的按钮，和/或利用`display: block`来实现。这个**购物车按钮**使用了“[自举](https://getbootstrap.com)配色；这实际上是:**蓝色**表示信息或主要按钮状态(例如 CTA)，而**绿色**表示成功状态(例如“事情已经完成”)，而**红色**表示危险状态(例如“小心考虑此选项”)。

如果你的电子商务应用有某种愿望清单或“保存以备后用”功能，产品图片右上角的**收藏夹按钮**会很有帮助。收藏夹和购物车按钮都使用[字体图标](https://fontawesome.com/icons)和颜色来更好地传达按钮和/或其状态的信息，即使人们可能看不懂标签。

下面是一张 GIF 图，显示了购物车按钮和收藏按钮的不同状态:

[![ecommerce listing animated GIF showing different states of the "Add to Cart" button and "favorite" button](../Images/8d8968ed071addf073958c289516346f.png)T2】](https://i.giphy.com/media/1wXbgS8b4A4ObXbZ4P/giphy.gif)

您可以在我的 Github pages 站点上查看我为这个项目编写的代码实现[。](https://geoffdavis92.github.io/weekly-ui/)

## 现在轮到你了

我使用了 [React.js](https://reactjs.org) 和 [Storybook](http://storybook.js.org) 来开发我的实现，但是你可以使用任何你喜欢的技术栈！(提示:如果你用 [Vue.js](https://vuejs.org/) 或者 [Angular.js](https://angularjs.org) ，那些库还是可以用 [Storybook 的)](https://storybook.js.org/basics/slow-start-guide/)

如果你不愿意，你甚至可以不使用视图库；HTML 和 CSS-only(和非视图 JavaScript 库)组件是可能的，特别是对于这一步。

此外，请在评论中添加您的设计灵感的回复和/或图像！我很想看看你们都创作了什么样的设计。

编码快乐！🎉

### 第 1 周日历

1.  设计组件✅
2.  显示产品名称、价格和✅图片
3.  添加到购物车按钮，收藏按钮🎯
4.  销售价格显示，售完状态
5.  颜色变化缩略图按钮
6.  100% a11y 分数
7.  调整、重构、修复

### 资源

*   [Fontawesome 图标](https://fontawesome.com) -免费和专业(付费)图标
*   [Learnstorybook.com](https://dev.to/chroma/introducing-learnstorybookcom-1k6d)-使用来自 [Chroma](http://chromaticqa.com) 的免费用户界面教程学习故事书
*   [按钮 UX 设计:最佳实践、类型和状态](https://uxplanet.org/button-ux-design-best-practices-types-and-states-647cf4ae0fc6)