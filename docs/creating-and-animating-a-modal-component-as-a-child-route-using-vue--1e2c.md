# 使用 Vue 创建一个模态组件并制作动画作为子路径

> 原文：<https://dev.to/ramsay/creating-and-animating-a-modal-component-as-a-child-route-using-vue--1e2c>

> [这里是回购的链接。](https://github.com/ramsaylanier/vue-tutorials/tree/master/animating-child-route)
> [下面是现场演示。](https://ramsaylanier.github.io/vue-tutorials/animating-child-route/#/)

当我从事副业时，我有时会偶然发现一个我非常喜欢的模式。这是其中的一次。今天，我将向您展示如何使用子路由将列表项的详细视图实现为模态视图。

如果你玩一下[现场演示](https://ramsaylanier.github.io/vue-tutorials/animating-child-route/#/),你会注意到当一个规则被点击并且一个模态呈现时，URL 会更新。这意味着我们可以刷新页面，模式将在后台呈现规则列表。这是一个很好的接触，因为我们总是能够在游戏的上下文中看到规则。

### 路由器