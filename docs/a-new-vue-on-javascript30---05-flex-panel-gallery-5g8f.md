# JavaScript 30-05 Flex Panel Gallery 上的新 Vue

> 原文：<https://dev.to/davefollett/a-new-vue-on-javascript30---05-flex-panel-gallery-5g8f>

本文是 JavaScript30 系列的 [A New Vue 的一部分，该系列探索了使用](https://dev.to/t/anewvueonjs30) [Vue](https://vuejs.org) 重新实现韦斯博斯([@韦斯博斯](https://twitter.com/wesbos) ) [#JavaScript30](https://JavaScript30.com) 项目。今天我将处理#JavaScript30 的 **05 - Flex Panel Gallery** 项目。这个项目显示了一页由五张图片组成的垂直切片，上面有文字。当一个图像被点击时，会有一个简洁的动画来展开图像并滑入一些附加的文本。我的描述没有做到公正，所以动画 gif 怎么样？

[![#JavaScript30 Finished Flex Panel Gallery](img/905238866050fa5a14f252fb73ab0759.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9XWSIeO3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://davefollett.io/2018/10/09/a-new-vue-on-javascript30-05-flex-panel-gallery/js30-flex-panel-gallery.gif)

如果你一直在关注，你会注意到我跳过了#JavaScript30 的 **04 - Array Cardio Day 1** 项目。虽然这是学习 [JavaScript 数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)的一个很好的练习，但是用 Vue 重新实现是没有意义的，因为页面上什么都没有呈现。

## 🔑“概念”视图

*   `v-for`指令
*   类绑定
*   事件绑定

## 🏗️ Vue 实施

第一步和我的其他文章一样，从我的[入门](https://dev.to/davefollett/a-new-vue-on-javascript30---getting-started-4o61)文章中抓取[基础启动文件](https://github.com/davefollett/JavaScript30/blob/master/00%20-%20Getting%20Started/index-VUE.html)，并将原始#JavaScript30 项目中的代码插入到它们对应的 Vue 位置。

[![Animation of inserting #JavaScript30 code into their corresponding Vue locations](img/fb3175b7906ff697099ba6627d4167e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8rgwAuTe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://davefollett.io/2018/10/09/a-new-vue-on-javascript30-05-flex-panel-gallery/js30-to-vue-locations.gif)

*   HTML 部分被放在根目录`<div id="app">`中
*   这些功能被放入`methods`部分
*   由于不需要，`computed`、`mounted`和`watch`部分被删除
*   `<style>`部分保持不变

从这里开始，我的方法和我做 [JavaScript Drum Kit](https://dev.to/davefollett/a-new-vue-on-javascript30---01-javascript-drum-kit-1k7f) 项目时的方法差不多。首先，获取重复的 HTML 部分，并将它们转换成一个对象数组。