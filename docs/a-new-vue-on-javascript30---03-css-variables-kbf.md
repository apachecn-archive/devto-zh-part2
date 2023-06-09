# JavaScript30 - 03 CSS 变量上的新 Vue

> 原文：<https://dev.to/davefollett/a-new-vue-on-javascript30---03-css-variables-kbf>

本文是 JavaScript30 系列的 [A New Vue 的一部分，该系列探索了使用](https://dev.to/t/anewvueonjs30) [Vue](https://vuejs.org) 重新实现 [#JavaScript30](https://JavaScript30.com) 项目。今天，我们将讨论第三个 Wes Bos 的([@ Wes Bos](https://twitter.com/wesbos))[# JavaScript 30](https://JavaScript30.com)项目，标题为: **03 - CSS 变量**。这个项目使用一个 HTML 输入来控制 [CSS 变量](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables)来改变图像的模糊、填充和背景颜色。

## 关键 Vue 概念

本文讨论了以下 Vue 概念:

*   用`:style`(对`v-bind:style`的简写)进行样式绑定
*   `v-model`在表单输入上创建双向数据绑定的指令
*   计算属性
*   受关注的属性
*   `mounted`生命周期挂钩

## 接近

着手做这件事时，我很矛盾。我应该忠于#JavaScript30 并使用 CSS 变量还是利用 Vue 的特性重新实现它？举棋不定的我走上[CodingBlocks.net 松弛频道](https://www.codingblocks.net/slack/)通过民意调查来征求意见。最终结果是:

*   1 为 **CSS 变量**投票
*   为 **Vue 的功能**投 1 票
*   5 票赞成**两者兼而有之**

哦，我还得到了一张选票:

> 对样式化组件使用 React:P ~ MinimumViablePerson

不打算说谎，我有一个很好的窃笑，当我读到这一点。自从我开始用 Vue 重做#JavaScript30 以来，我收到了相当多的评论，有人应该用 React 做同样的事情。我很想看看它是如何比较的，所以也许是时候开始计划我的下一个博客系列了…

好，就这么定了。让我们双管齐下。

## CSS 变量

第一步和我的其他文章一样，从我的[入门](https://dev.to/davefollett/a-new-vue-on-javascript30---getting-started-4o61)文章中抓取[基础启动文件](https://github.com/davefollett/JavaScript30/blob/master/00%20-%20Getting%20Started/index-VUE.html)，并将原始#JavaScript30 项目中的代码插入到它们对应的 Vue 位置。

*   HTML 部分放在根目录`<div id="app">`中
*   该功能进入`methods`部分
*   `<style>`部分被移到了我的 Vue 版本中
*   因为不需要，所以删除了`computed`部分

和其他文章一样，这让我们找到了一个可行的解决方案，但还不够。我们还有一些工作要做。首先，我向 Vue 实例数据对象添加了三个字段:`spacing`、`blur`和`base`，并赋予它们初始值以匹配#JavaScript30