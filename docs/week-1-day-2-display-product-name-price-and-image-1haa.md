# 第 1 周第 2 天:显示产品名称、价格和图片

> 原文：<https://dev.to/geoff/week-1-day-2-display-product-name-price-and-image-1haa>

欢迎来到我每周 UI 挑战的第一周第二天！正如我在的
[公告中所说，第一周将集中在**电子商务列表** UI
组件上；接下来一周的每一天，我都会挑选一两个
(通常是相关的)子元素来实现。第二天，我们的目标是…](https://dev.to/geoff/announcing-weekly-ui-challenge-h87)

## 显示商品名称、价格和图片

既然我们的设计已经准备好了，是时候开始实现一些特性了。对于任何类型的电子商务组件来说，一个好的起点是展示客户购买产品时需要知道的最基本的东西:产品的名称、价格，也许还有与产品相关的照片或图片。

我正在根据我在 Unsplash 上找到的一个帽子的[照片制作我的组件；这是一张很棒的照片，免费，(而且](https://unsplash.com/photos/GsKf0FXVj3Y)[得到了许可](https://unsplash.com/license))所以我决定在我的产品中使用它。 **Thinsulate 冬帽**是 *you* 的完美低价童帽/平底雪橇/【在此插入地区冬季针织帽名称】，现在我的工作是让它在电子商务平台上上市，这样人们就可以真正购买它。

根据我创作的原始设计，这是我为第二天准备的:

[![ecommerce listing showing orange knitted winter hat, the name of item "Thinsulate Winter Cap", and the $34.99 price](../Images/e451d8e5cbc319a6ff7647a52783729f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QfRjgyhd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/geoffdavis92/weekly-ui-assets/master/ecommerce-listing/day2/w1d2-final.png)

我对容器元素采用了经典的“卡片”风格设计，在内部内容周围留有足够的空白填充；它有一个微妙的 **2px** `border-radius`来软化边缘，但不足以使它看起来“幼稚”或畸形。如果你仔细观察，你会看到我在产品图像上添加了相同的半径，这样可以使两个元素看起来统一。

我决定使用 **20px** 的基底`font-size`，这是为了可读性和视觉冲击[推荐的正文字体大小](https://blog.usejournal.com/your-body-text-is-too-small-5e02d36dc902)。(阅读那篇文章，它有一些其他的优点)对于我的`font-family`，我使用 **Droid Sans** 作为首选字体，使用 **Roboto** 和 system sans-serif 字体作为备用字体。

产品标题和副标题分别使用语义标题标签:`h4`和`h5`；这有利于 SEO 和 a11y 评级，但可能不是必要的，因为适当对比的字体大小和粗细可以达到目的。标题和价格元素比基本尺寸稍大，以传达重要性，出于同样的原因，它们也利用了更高的`font-weight`，并在扫描时有助于可读性。

您可以在我的 Github pages 站点上查看我为这个项目编写的代码实现[。](https://geoffdavis92.github.io/weekly-ui/)

## 现在轮到你了

我使用了 [React.js](https://reactjs.org) 和 [Storybook](http://storybook.js.org) 来开发我的实现，但是你可以使用任何你喜欢的技术栈！(提示:如果你用 [Vue.js](https://vuejs.org/) 或者 [Angular.js](https://angularjs.org) ，那些库还是可以用 [Storybook 的)](https://storybook.js.org/basics/slow-start-guide/)

如果你不愿意，你甚至可以不使用视图库；HTML 和 CSS-only(和非视图 JavaScript 库)组件是可能的，特别是对于这一步。

此外，请在评论中添加您的设计灵感的回复和/或图像！我很想看看你们都创作了什么样的设计。

编码快乐！🎉

### 第 1 周日历

1.  设计组件✅
2.  显示产品名称、价格和图像🎯
3.  添加到购物车按钮，收藏按钮
4.  销售价格显示，售完状态
5.  颜色变化缩略图按钮
6.  100% a11y 分数
7.  调整、重构、修复

### 资源

*   [如何正确使用 H1-H6 HTML 元素](https://www.hobo-web.co.uk/headers/)
*   [您的正文太小](https://blog.usejournal.com/your-body-text-is-too-small-5e02d36dc902)
*   [故事书](https://storybook.js.org) - JavaScript 视图库开发环境
*   [Unsplash](https://unsplash.com) -免费且未经许可的高质量图像