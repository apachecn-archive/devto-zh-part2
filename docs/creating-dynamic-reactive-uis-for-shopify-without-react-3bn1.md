# 为 Shopify 创建动态、反应式用户界面，无需反应

> 原文：<https://dev.to/pbj/creating-dynamic-reactive-uis-for-shopify-without-react-3bn1>

互联网上有很多网站依靠 Shopify 来满足他们的电子商务需求。几乎所有这些网站都有交互功能，这些功能负责一些基于 UI 的更新。管理不同 JavaScript 文件之间的 UI 更新有时会失控，导致代码杂乱无章或紧密耦合。

我最近建立的网站，猫猴使用了 Shopify，我在计划建立的时候有了这个想法。我知道我需要在组件和整个站点之间共享组件状态。我考虑过添加 React，但后来决定通过事件库使用发布者-订阅者模式可能是一种更简单的方法。正如大多数 clickbait 文章所说:*结果会让你震惊！*

## 在 Shopify 中使用事件

当我发现 Shopify 在 Slate 中预装了一个事件库时，我感到很惊讶。通过 Webpack，Shopify 附带了一个事件模块“events.js”，它为浏览器环境实现了 [Node.js 事件模块。](https://www.npmjs.com/package/events)

这种方法也可以用任何其他事件模块来实现。

## 动态、反应式用户界面

我为 Colugo 构建电子商务网站的目标是尽可能减少组件之间的直接耦合。使用发布者-订阅者模式来管理事件使解耦变得轻而易举，并以更有组织的代码库结束。

预先知道我将使用这种模式使我能够规划出一些需要动态 UI 组件的组件以及一些状态:

*   当用户向购物车添加商品时
    *   如果成功，显示迷你卡和/或确认消息
    *   如果不成功，显示错误消息。
*   当用户点击移动“购物车”图标来显示菜单时
    *   如果移动菜单可见，请关闭它并显示迷你购物车
    *   如果移动菜单不可见，请打开迷你购物车。
*   当用户查看产品详细信息页面时
    *   如果用户选择产品颜色，则滚动:
        *   显示当前颜色选择
        *   用户可以更新选择
        *   共享组件之间反映的新选择
    *   如果用户更新颜色选择:
        *   显示当前数量
        *   当用户向上滚动时，新数量应该正确显示在共享组件之间

如果我们使用 React，许多 UI 交互将通过 Redux 或 ad HOC 将道具和事件处理程序传递给更小的组件。由于我们不能轻松地使用 React 和 Shopify，我们需要另一种方法来共享组件之间的小块“状态”更新。

## 输入 Event.js

### 为有组织的事件使用名称空间

因为我们的事件在多个模块中被触发，所以我们创建了一个小对象来公开事件“类型”。看起来是这样的:

```
export default eventTypes = {
    menuUpdate: 'colugo:menu-update',
    mobileMenuUpdate: 'colugo:mobile-nav-update',
    cartUpdate: 'colugo:cart-update',
    variantUpdate: 'colugo:variant-update',
    productNavUpdate: 'colugo:pn-udpate',
    miniCartUpdate: 'colugo:minicart-update',
    recircUpdate: 'colugo:recirc-update',
    recircCartUpdate: 'colugo:recirc-cart-add',
}; 
```

这个模块并不特殊。它提供了一种一致的方式来设置命名事件发布和订阅，我知道这不会在模块中发生冲突。

### 有效载荷

在每个事件中，我们传入一个 payload 对象，该对象表示任何和所有订户可以用来更新其 UI 的数据。就猫猴而言，有效载荷通常是:

*   产品
    *   不同的
        *   身份证明
        *   标题
        *   价格
        *   库存
        *   颜色
*   手推车
    *   总菌数
    *   项目数组
    *   购物车更新(成功或失败)
*   显示状态
    *   迷你推车切换
    *   Menu Toggling
    *   时事通讯模式

### 发布事件

现在我们已经设置了名称空间和有效负载，我们可以发布一个事件:

```
import events from 'events'
import eventTypes from 'eventTypes'

events.emit(eventTypes.variantUpdate, {
    ... payload object
}); 
```

### 订阅事件

我们的活动总线现在使订阅活动变得非常简单。将我们的名称空间和有效负载结合在一起，订阅事件如下所示:

```
import events from 'events'
import eventTypes from 'eventTypes'

events.on(eventTypes.variantUpdate, (payload) => {
    ... run whatever you want
})); 
```

这将 UI 更新与事件本身分开。例如，在 Colugo 中，当用户选择不同的产品颜色时，用户界面会发生一些变化:

*   新颜色被选为活动颜色
*   产品变型名称已更新
*   产品价格已更新
*   产品库存可能会更新
*   产品展示区图片可能会更新。

在过去的项目中，我可能将所有的 UI 更改都写入一个“products.js”文件中，该文件封装了组成更改的 UI 的所有 DOM 元素。虽然这样做了，但我们还必须跟踪多个 UI 组件，跟踪哪个组件发生了更改，何时发生的更改，然后将更改传播到其他组件。简而言之，旧的方法意味着大量的数组和循环。

在 Colugo 的例子中，如果用户与颜色选择器交互，我们订阅`variantUpdate`事件并调用组件方法来更新 UI。

```
import events from 'events'
import eventTypes from 'eventTypes'

events.on(eventTypes.variantUpdate, (product) => {
    // destructure object for props if necessary
    //
    colorPicker.setActiveColor(product.variant.color)
    productContent.udpateTitle(product.title)
    productContent.updatePrice(product.price)
    productContent.updateInveotyr(product.inventoryCount)
    productGallery.updateGallery(product.variant.images)
    ...
    ...
});

By leveraging the publisher-subscriber pattern and the built-in events library in Webpack we’re able to manage UI changes in a clean, consistent and organized manner. Our footprint is kept to a minimum and UI components (existing or newly made) can easily subscribe to events with very little overhead. 
```

### 抛开“事件”

在本文中，我们使用术语“事件”。值得澄清的是，我们不是指浏览器事件，如“点击”或“onchange”事件。使用发布者-订阅者模式时，不会触发浏览器事件，因此对性能也有好处。

进一步阅读的有益参考:[https://stack overflow . com/questions/27677692/performance-cost-of-pubsub-excessive-events-and-event-handlers-in-JavaScript](https://stackoverflow.com/questions/27677692/performance-cost-of-pubsub-excessive-events-and-event-handlers-in-javascript)

#### IRL 示例

除了谈论作品，你还可以随意查看猫猴的产品详情页面并四处逛逛！您将看到 UI 的各个部分是如何相互作用的。下面是一些可以利用发布者-订阅者方法和 events.js 的地方

*   选择产品页面上的颜色
    *   这应该会改变图库和英雄中的一些内容
*   向下滚动并使用粘性拾色器更新颜色
    *   这将使内容和图库同步到新的当前选择
*   将产品添加到购物车-如果成功，将打开购物车并更新购物车数量

#### 民众

发布/子计数器示例

[https://codepen.io/pbj/embed/QZPJvR?height=600&default-tab=result&embed-version=2](https://codepen.io/pbj/embed/QZPJvR?height=600&default-tab=result&embed-version=2)

发布/子购物车通知

[https://codepen.io/pbj/embed/LgvMga?height=600&default-tab=result&embed-version=2](https://codepen.io/pbj/embed/LgvMga?height=600&default-tab=result&embed-version=2)

发布/子活动元素

[https://codepen.io/pbj/embed/oQZKRz?height=600&default-tab=result&embed-version=2](https://codepen.io/pbj/embed/oQZKRz?height=600&default-tab=result&embed-version=2)

非常感谢[齐·斯潘塞](http://www.zeespencer.com/)在发表前审阅和编辑这篇文章。