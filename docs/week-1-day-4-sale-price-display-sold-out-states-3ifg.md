# 第 1 周第 4 天:销售价格显示，售完状态

> 原文：<https://dev.to/geoff/week-1-day-4-sale-price-display-sold-out-states-3ifg>

欢迎来到我每周 UI 挑战的第 1 周第 4 天！正如我在[公告帖子](https://dev.to/geoff/announcing-weekly-ui-challenge-h87)中所说的，第一周将关注**电子商务列表** UI 组件；在接下来的一周里的每一天，我都会挑选一两个(通常是相关的)子元素来实现。第四天，我们的目标是…

## 销售价格显示，售完状态

有时候，当你是一个企业主，你必须让顾客对你的产品产生兴趣，或者激励顾客购买产品。对于实体商店来说，这可能是一个华而不实的标志或者是一个庸俗的地方电视广告；对于在线商店，这可能是列表顶部的一个横幅，一个装饰产品卡上角的丝带，或者列表上的一个**销售**标注，这是我已经配置好的。

相反，在你的顾客点击进入产品页面之前，告诉他们一件商品是否有货是件好事。为了帮助我的用户搜索他们实际可以购买的产品，我添加了一个**售罄**标注来代替价格。

根据我创作的原始设计，这是我为第四天准备的:

[![ecommerce listing showing orange knitted winter hat, the name of item "Thinsulate Winter Cap", and the $20.99 sale price with a crossed out $34.99 price, and an "add to cart" button in blue, on the bottom](../Images/be39cf0226df00724a27b26bea87cd1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LQYQNV7n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/geoffdavis92/weekly-ui-assets/master/ecommerce-listing/day4/w1d4-final-group.png)

对于销售价格显示，我添加了一个绿色标注，或“徽章”，有时也称为。然后，我很自然地在它旁边显示销售价格；为了结束销售更新，我删除了原价，这样用户就可以*看到*他们的节省了！(我们在这里谈论的是*字面上的*大幅降价)

对于售罄的显示，我实际上使用了相同的徽章组件和不同的道具来使边框/文本颜色为红色。无需显示此产品的价格，因为它不出售；如果这是一个更大的电子商务平台，你仍然可以/应该将组件链接到产品页面，并在那里显示价格，为好奇的头脑和长期规划者。

最后，为了确保电子商务数据库团队不需要进入并删除一些订单和发布道歉电子邮件，我需要禁用按钮及其功能。因为这是一个语义上的`button`元素，我可以添加`disabled`属性(或者因为我正在使用 React，一个“prop”)来使按钮不可点击；为了安全起见，我还移除了`onClick`处理程序，并为视觉购物者设置了灰度按钮。

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
4.  销售价格显示，售完状态🎯
5.  颜色变化缩略图按钮
6.  100% a11y 分数
7.  调整、重构、修复