# 第 1 周第 5 天:颜色变化缩略图按钮

> 原文：<https://dev.to/geoff/week-1-day-5-color-variant-thumbnail-buttons-4epi>

欢迎来到我每周 UI 挑战的第 5 天，第 1 周！正如我在[公告帖子](https://dev.to/geoff/announcing-weekly-ui-challenge-h87)中所说的，第一周将关注**电子商务列表** UI 组件；在接下来的一周里的每一天，我都会挑选一两个(通常是相关的)子元素来实现。第五天，我们的目标是…

## 颜色变体缩略图按钮

他们说“世界不是非黑即白”，这肯定是真的；那么，为什么我们的电子商务产品只限于一种颜色呢？今天我们将添加一个原始产品的变体的小列表；这样的组件可以在 [J.Crew](https://www.jcrew.com/c/mens_category/teesfleece) (悬停在一个产品上) [Madewell](https://www.madewell.com/madewell_category/SHOESANDBOOTS.jsp) 等网店上看到。这是另一个元素，不仅对你的用户来说很好，而且对展示所有产品选项和可用性来增加转化率也很重要。

根据我创作的原始设计，这是我为第五天准备的:

[![ecommerce listing showing orange knitted winter hat, the name of item "Thinsulate Winter Cap", and the $20.99 sale price with a crossed out $34.99 price, and an "add to cart" button in blue, on the bottom](img/985a01f1af760cf6a97161d501e158ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uPHFBnFo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/geoffdavis92/weekly-ui-assets/master/ecommerce-listing/day5/w1d5-final.png)

我把这个变体列表放在产品名称和价格的下面——但是在购物车按钮的上面——因为它更像是那些文本元素的次要特性，应该放在可操作的 UI 项目之前，这可能会把用户从页面上带走(购物车按钮)。

为了突出显示当前选中的变体，我在缩略图周围放置了一个边框，并淡出了未选中的选项。最后，一旦商品进入购物车，变体就会被锁定，在变体选择器周围会有一个适当的光标来指示这一点。

下面是变体选择器的 GIF 图片:

[![Ecommerce listing GIF animation showing the different states of the variant selector, as it loops through various colors of the hat product](img/565c762d565dc7249064d2998fb5cafe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nl0XaQus--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/geoffdavis92/weekly-ui-assets/master/ecommerce-listing/day5/w1d5-variant-demo-ld.gif)

您可以在我的 Github pages 站点上查看我为这个项目编写的代码实现[。](https://geoffdavis92.github.io/weekly-ui/)

## 现在轮到你了

我使用了 [React.js](https://reactjs.org) 和 [Storybook](http://storybook.js.org) 来开发我的实现，但是你可以使用任何你喜欢的技术栈！(提示:如果你用 [Vue.js](https://vuejs.org/) 或者 [Angular.js](https://angularjs.org) ，那些库还是可以用 [Storybook 的)](https://storybook.js.org/basics/slow-start-guide/)

如果你不愿意，你甚至可以不使用视图库；HTML 和 CSS-only(和非视图 JavaScript 库)组件是可能的，特别是对于这一步。

此外，请在评论中添加您的设计灵感的回复和/或图像！我很想看看你们都创作了什么样的设计。

编码快乐！🎉

### 第 1 周日历

1.  设计组件✅
2.  显示产品名称、价格和✅图片
3.  添加到购物车按钮，收藏夹按钮✅
4.  销售价格显示，售罄状态✅
5.  颜色变化缩略图按钮🎯
6.  100% a11y 分数
7.  调整、重构、修复